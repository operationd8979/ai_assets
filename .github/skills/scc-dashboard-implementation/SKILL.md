---
name: scc-dashboard-implementation
description: "Implement a new SCCVision Angular dashboard with the filter hook pattern and custom widget components. Use when: creating a new dashboard feature, adding widgets to an existing dashboard, wiring up useDashboardFilter or useWidgetFilter, building stat/chart/table widget data components, registering widgets with WIDGET_TOKEN, or understanding the filter-to-widget communication flow."
argument-hint: "Describe the new dashboard or widget to implement (e.g. 'storage performance dashboard with bandwidth stat widgets')"
---

# SCCVision Dashboard Implementation

---

## Pre-Implementation: Required Information

**Before writing any code**, collect ALL required fields below. If the user's prompt is missing any required item, ask for it explicitly. Do not assume or invent values.

Use this checklist as a Q&A template — ask as a single grouped message covering all missing items at once.

---

### Dashboard-Level (ask once)

| # | Field | Required | Example |
|---|-------|----------|---------|
| 1 | **Dashboard name / domain** | ✅ Required | `risk`, `storage`, `network-performance` |
| 2 | **Filter fields** — property name, display label, filter type (`MultiSelect`, `DateRange`, etc.) | ✅ Required | `owner (MultiSelect)`, `status (MultiSelect)`, `date (DateRange)` |
| 3 | **Base model / DB schema** — field names and types the API returns | Optional | `{ id, title, status, owner, createdAt }` |
| 4 | **Common request body** shared by all widgets (if applicable) | Optional | `{ tenantId, startDate, endDate, status[] }` |
| 5 | **Common response shape** shared by all widgets (if applicable) | Optional | `{ data: Item[], total: number }` |

---

### Per-Widget (repeat for every widget)

| # | Field | Required | Example |
|---|-------|----------|---------|
| 1 | **Widget name** (becomes selector & class name) | ✅ Required | `Monthly Risks by Status` |
| 2 | **Widget type** | ✅ Required | `stat`, `line chart`, `bar chart`, `multi-series chart`, `pie`, `gauge`, `gauge needle`, `analytics gauge`, `radar`, `score gap radar`, `square`, `percentage breakdown`, `map`, `table` |
| 3 | **Insight / purpose** — what the widget communicates | ✅ Required | `Display risk items grouped by status over time` |
| 4 | **Request body** (if no common setting or differs from it) | ✅ Required when no common | `{ groupBy: 'status', aggregateType: 'count' }` |
| 5 | **Response shape** (if no common setting or differs from it) | ✅ Required when no common | `{ data: [{ label, value }] }` |
| 6 | **Dataset mapping** | ✅ Required | Axis Y: number of items; Axis X: month (time) |
| 7 | **Click-to-filter behavior** — what filter does a click apply? | Optional | Clicking a point filters by `date`; clicking a bar filters by `owner` |

---

### Example Prompt (well-formed)

```
Implement 'risk' dashboard

1. Filters: owner (MultiSelect), status (MultiSelect), date (DateRange)
2. Base model: { id, title, status, owner, createdAt }
3. Common request body: { tenantId, startDate, endDate, resourceGroup, reourceName, owner }
4. Common response: { data: RiskItem[] }

Widgets:
  - Name: "Monthly Risks by Status"
    Type: line chart
    Insight: show risk count per status per month
    Request body: { ...common, groupBy: ['status'] }
    Response: { data: [{ month, status, count }] }
    Dataset: X = month (time), Y = count (number)
    Click data: filter by date (DateRange)

  - Name: "Open Risks Split by Owners"
    Type: bar chart
    Insight: show open risk count per owner
    Request body: { ...common, status: 'open', groupBy: ['owner'] }
    Response: { data: [{ owner, count }] }
    Dataset: X = owner (label), Y = count (number)
    Click data: filter by owner (MultiSelect)

  - Name: "List of Open Risks By Owner"
    Type: table
    Insight: show details of open risks
    Request body: { ...common, groupBy: ['resource group'] }
    Response: { data: [{ resourceGroup, title, status, owner, createdAt }] }
    Dataset: columns = name (resourceGroup), title, createdAt
    Click data: filter by level row 0 = resourceGroup, row 1 = resourceName
    Drilldown: clicking a row trigger load data
    +request body: current request body with additional filter resourceGroup and groupBy resourceName
    +response: { data: [{ resourceName, title, status, owner, createdAt }] }
```

---

### Question Template (use when prompt is incomplete)

If the user's request is missing information, ask in one message:

```
To implement the [DOMAIN] dashboard I need a few more details:

Dashboard level:
- [ ] What filters should the filter panel include? (property name, label, type: MultiSelect / DateRange)
- [ ] Is there a common request body structure shared by all widgets?
- [ ] Is there a common response shape shared by all widgets?

For each widget, please provide:
- Widget name
- Widget type (line chart / bar chart / stat / pie / gauge / table / etc.)
- Insight (what should it communicate?)
- Request body (or "same as common" + any overrides)
- Response shape (or "same as common")
- Dataset mapping (what goes on each axis / what value does each segment represent?)
- Click-to-filter behavior (optional — what filter should clicking a data point apply?)
- Drilldown behavior (only for tables — should clicking a row trigger loading new data? If so, what additional request parameters and response shape should be expected?)
```

---

## When to Use
- Creating a new Angular dashboard component with a filter panel
- Adding new widget components to an existing dashboard
- Wiring the `useDashboardFilter` / `useWidgetFilter` filter hooks
- Implementing a `WidgetBaseDataComponent` subclass for data loading
- Registering widgets with `WIDGET_TOKEN` in an Angular module
- Understanding the bi-directional filter communication pattern

---

## Architecture Overview

```
Dashboard Component (useDashboardFilter)
│
├── Filter Panel ──StateManager.SharedEvent$(FilterApply)──► publishFilterSettings
│                                                                     │
│   ◄──────────────────────CommunicationService────────────────────────┤
│                          'FilterApply' channel                       │
│                                                                     ▼
└── Widgets (useWidgetFilter)
      - ignore self-published messages (widgetId guard)
      - transform FilterSetting[] → request body
      - trigger data load via combineLatest([tenantId$, body$])
      - emit click filters back on 'ClickedEvent' channel
```

---

## Step-by-Step: New Dashboard

### 1. Create the Domain Constants File

Define filter key constants, field name map, and any shared domain enums:

```typescript
// my-domain.constants.ts
export const AVAILABLE_FILTER_KEYS = {
  timeRange: 'date',
  resource:  'resourceShortName',
  // add domain-specific keys
};

export const FieldMyDomain = {
  Resource:      'resourceShortName',
  Date:          'date',
  SomeMetric:    'someMetric',
};
```

### 2. Create the Domain Model File

```typescript
// my-domain.model.ts
export interface MyDomainBody {
  startDate?:     string;
  endDate?:       string;
  resourceNames?: string[];
}

export interface MyDomainItem {
  someMetric?: number;
  // other API response fields
}
```

### 3. Create the Domain Service

Always define the API method signature first, then put mock data behind a `USE_MOCK` flag. When the real API is ready, flip the flag to `false` — no other code changes needed.

```typescript
// my-domain.service.ts
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, of, delay } from 'rxjs';

// ─── Toggle this flag to switch between mock and real API ───────────────────
const USE_MOCK = true;
// ────────────────────────────────────────────────────────────────────────────

@Injectable()
export class MyDomainService {
  private readonly http = inject(HttpClient);

  // ── Mock data ─────────────────────────────────────────────────────────────

  private readonly MOCK_MY_DATA: { data: MyDomainItem[] } = {
    data: [
      { label: 'Jan 2026', value: 12, category: 'open' },
      { label: 'Feb 2026', value: 19, category: 'closed' },
      { label: 'Mar 2026', value: 7,  category: 'open' }
    ]
  };

  private readonly MOCK_FILTERS: { resourceNames: string[] } = {
    resourceNames: ['Router-A', 'Router-B', 'Switch-C']
  };

  // ── API methods ───────────────────────────────────────────────────────────

  getMyData(tenantId: string, body: MyDomainBody): Observable<any> {
    if (USE_MOCK) return of(this.MOCK_MY_DATA).pipe(delay(300));
    return this.http.post(`/api/tenants/${tenantId}/my-data`, body);
  }

  getFilters(tenantId: string): Observable<any> {
    if (USE_MOCK) return of(this.MOCK_FILTERS).pipe(delay(200));
    return this.http.get(`/api/tenants/${tenantId}/my-data/filters`);
  }
}
```

**Rules for mock data:**
- Mock shape MUST exactly match the real API response shape — same field names, same nesting.
- Type method must return as `Observable<any>` to avoid over-specifying types.
- Use `delay(ms)` to simulate network latency so loading spinners are visible during development.
- Keep `USE_MOCK` at the top of the file so it is easy to find and flip.
- Remove the mock block (and `USE_MOCK` flag + `delay` import) before merging to production.

### 4. Create the Dashboard Component

```typescript
// my-dashboard.component.ts
import { useDashboardFilter, Filter, FilterType, DateRangeFilter, MultiSelectFilter,
         isMultiSelectFilter, FilterOption } from '@sccvision/core';
import { StateManager, AppStateNames } from '@sccvision/common';
import { WidgetContainerComponent } from '@sccvision/widgets';

const FILTER: Filter[] = [
  {
    key: AVAILABLE_FILTER_KEYS.timeRange,
    label: 'Date',
    type: FilterType.DateRange,
    dateFrom: new Date(new Date().setMonth(new Date().getMonth() - 3)),
    dateTo: new Date(),
    isHidden: false
  } as DateRangeFilter,
  {
    key: AVAILABLE_FILTER_KEYS.resource,
    label: 'Resource Name',
    type: FilterType.MultiSelect,
    data: [],
    isHidden: false,
    isAllowedToSelectAll: true
  } as MultiSelectFilter,
];

@Component({
  selector: 'app-my-dashboard',
  imports: [WidgetContainerComponent],
  providers: [MyDomainService],
  templateUrl: './my-dashboard.component.html'
})
export class MyDashboardComponent extends BaseComponent implements OnInit, OnDestroy {
  private readonly subMain = new Subscription();
  private readonly filterClient = useDashboardFilter();

  constructor(
    override state: StateManager,
    private readonly myDomainService: MyDomainService
  ) { super(state); }

  ngOnInit(): void {
    this.filterClient.init(FILTER);     // wires 3 subscriptions; pass isToPush=true to broadcast immediately
    this.setupLoadCategories();
  }

  private setupLoadCategories(): void {
    this.subMain.add(
      this.state.getStatebyName(AppStateNames.Customer).pipe(
        debounceTime(150),
        filter(s => !!s?.tenantId),
        map(s => s.tenantId),
        switchMap(tenantId =>
          this.myDomainService.getFilters(tenantId).pipe(catchError(() => of({ resourceNames: [] })))
        )
      ).subscribe(response => {
        this.filterClient.patchFilter(state => ({
          ...state,
          filters: state.filters.map(f =>
            isMultiSelectFilter(f) && f.key === AVAILABLE_FILTER_KEYS.resource
              ? { ...f, data: response.resourceNames.map(n => ({ key: n, label: n, checked: false } as FilterOption)) }
              : f
          )
        }));
      })
    );
  }

  ngOnDestroy(): void { this.subMain.unsubscribe(); }
}
```

**Dashboard template:**

```html
<!-- my-dashboard.component.html -->
<wgt-widget-container widgetName="my-stat-widget"></wgt-widget-container>
<wgt-widget-container widgetName="my-line-chart-widget"></wgt-widget-container>
```

### 5. Create the Domain Widget Base Data Component

Extend `WidgetBaseDataComponent` (app-level abstract) to add domain-specific trigger sources and `buildRequestBody`:

```typescript
// widgets/my-widget-base-data.component.ts
import { WidgetBaseDataComponent } from '../../shared/widget-base-data.component';
import { AppStateNames, FilterSetting, StateManager } from '@sccvision/common';

export abstract class MyDomainWidgetBaseDataComponent
    extends WidgetBaseDataComponent implements OnInit {

  protected readonly myDomainService = inject(MyDomainService);
  protected readonly state = inject(StateManager);

  override getTriggerSources(): Record<string, Observable<any>> {
    return {
      tenantId: this.state.getStatebyName(AppStateNames.Customer).pipe(
        debounceTime(150),
        distinctUntilChanged((a: any, b: any) => a?.tenantId === b?.tenantId),
        map(s => s?.tenantId),
        tap(() => {
          // Reset stale filter values on tenant change
          this.requestBodySubject.next({ ...this.requestBodySubject.value, resourceNames: [] });
        })
      ),
      body: this.requestBodyObservable$
    };
  }

  override buildRequestBody(filterSettings: FilterSetting[]): MyDomainBody {
    const body: MyDomainBody = {};
    const timeRange = filterSettings.find(f => f.property === AVAILABLE_FILTER_KEYS.timeRange);
    const resource  = filterSettings.find(f => f.property === AVAILABLE_FILTER_KEYS.resource);

    if (timeRange?.range?.from instanceof Date && timeRange?.range?.to instanceof Date) {
      body.startDate = timeRange.range.from.toFormattedString();
      body.endDate   = timeRange.range.to.toFormattedString();
    }
    if (resource?.values?.length) {
      body.resourceNames = resource.values as string[];
    }
    return body;
  }
}
```

### 6. Create Individual Widget Components

**Stat Widget:**

```typescript
// widgets/my-stat/my-stat.component.ts
const SELECTOR = 'my-stat-widget';

@Component({
  selector: SELECTOR,
  providers: [MyDomainService],
  imports: [StatWidgetComponent],
  templateUrl: './my-stat.component.html'
})
export class MyStatComponent extends MyDomainWidgetBaseDataComponent {
  dataSource: any;

  constructor() {
    super();
    this.filterClient = useWidgetFilter({ widgetId: SELECTOR });
    this.configuration = {
      title: 'My KPI',
      subtitle: 'units',
      subtitleType: SubtitleType.success,
      icon: 'chart-line'
    };
  }

  override loadData(triggers: Record<string, any>): Observable<any> {
    const { tenantId, body } = triggers;
    if (!tenantId) return of(null);
    return this.myDomainService.getMyData(tenantId, body).pipe(
      map(r => r?.data),
      catchError(() => of(null))
    );
  }

  override applyData(data: MyDomainItem[]): void {
    if (!data?.length) { this.dataSource = {}; return; }
    const total = data.reduce((acc, item) => acc + (item.someMetric ?? 0), 0);
    this.dataSource = { value: this.formatNumber(total) };
  }
}
```

**Stat Widget template:**

```html
<!-- my-stat.component.html -->
<wgt-stat-widget [dataSource]="dataSource" [settings]="$configuration | async"></wgt-stat-widget>
```

**Line Chart Widget with Click-to-Date-Filter** (extends `MyDomainLineWidgetDataComponent`):

> **Prerequisite:** Create `MyDomainLineWidgetDataComponent` that extends `NetworkPerformanceLineWidgetDataComponent`.  
> The line base class provides `lineDataSource`, `clickDataBehavior`, `onClickHandler()`, and `clickData$` subscription that draws a highlight annotation and applies a date-range filter on click.

```typescript
// widgets/my-line-chart/my-line-chart.component.ts
import { NetworkPerformanceLineWidgetDataComponent } from '../network-line-widget-data.component';
import { LineChartComponent, SubtitleType } from '@sccvision/widgets';
import { useWidgetFilter } from '@sccvision/core';

const SELECTOR = 'my-line-chart-widget';

@Component({
  selector: SELECTOR,
  providers: [MyDomainService],
  imports: [LineChartComponent],
  templateUrl: './my-line-chart.component.html'
})
export class MyLineChartComponent extends NetworkPerformanceLineWidgetDataComponent
    implements OnInit, OnDestroy {

  constructor() {
    super();
    this.filterClient = useWidgetFilter({ widgetId: SELECTOR });
    this.configuration = {
      title: 'My Throughput Trend',
      subtitle: 'Mbps',
      subtitleType: SubtitleType.success,
      icon: 'trending_up'
    };
  }

  override loadData(triggers: Record<string, any>): Observable<any> {
    const { tenantId, body } = triggers;
    if (!tenantId) return of([]);
    // Fetch time-series data grouped by date, ordered chronologically
    return this.myDomainService.getMyTimeSeries(tenantId, {
      ...body,
      aggregateField: ['someMetric'],
      aggregateType: 'average',
      groupBy: 'date',
      paginator: { orderBy: 'date asc' }
    }).pipe(map(r => r?.data), catchError(() => of([])));
  }

  override applyData(data: MyDomainItem[]): void {
    // CRITICAL: always reset click state on data reload so stale highlight is cleared
    this.clickDataBehavior.next(undefined);

    if (!data?.length) {
      this.lineDataSource = {};
      return;
    }

    const sorted = [...data].sort((a, b) => new Date(a.date).getTime() - new Date(b.date).getTime());
    this.lineDataSource = {
      labels: sorted.map(d => new Date(d.date).toFormattedString('dd/MM/yyyy')), // display label
      labelDate: sorted.map(d => new Date(d.date)), // actual Date objects for filter range
      datasets: [{
        label: 'My Metric (unit)',
        data: sorted.map(d => d.someMetric)
        // borderColor / backgroundColor are auto-assigned from ChartColors
      }],
      aspectRatio: 1.2  // narrower than default 2; adjust per layout
    };
  }
}
```

**Line Chart template — wire `onClickHandler` to enable click-to-date-filter:**

```html
<!-- my-line-chart.component.html -->
<wgt-line-chart
  [dataSources]="lineDataSource"
  [settings]="$configuration | async"
  (clickElementEmitter)="onClickHandler($event)">
</wgt-line-chart>
```

**How click-to-date-filter works internally (from `NetworkPerformanceLineWidgetDataComponent`):**

### `NetworkPerformanceLineWidgetDataComponent`

Abstract base for all Network Performance line-chart widgets. Provides `lineDataSource`, `clickDataBehavior`, `onClickHandler()`, and a `clickData$` subscription that draws a vertical highlight annotation and applies a date-range filter on click. Subclasses only need to implement `loadData()` and `applyData()`.

```typescript
import { Component, OnInit } from '@angular/core';
import { BehaviorSubject } from 'rxjs';
import { AVAILABLE_FILTER_KEYS } from '../network-performance.constants';
import { buildHighlightLineAnnotation } from '@sccvision/core';
import { NetworkPerformanceWidgetBaseDataComponent } from './network-widget-base-data.component';
import { FilterSetting } from '@sccvision/common';
import { ClickedData } from '@sccvision/widgets';

export abstract class NetworkPerformanceLineWidgetDataComponent extends NetworkPerformanceWidgetBaseDataComponent implements OnInit {
	protected lineDataSource: any;

	protected readonly clickDataBehavior = new BehaviorSubject<ClickedData | null | undefined>(undefined);
	protected readonly clickData$ = this.clickDataBehavior.asObservable();

	override ngOnInit(): void {
		super.ngOnInit();

		this.subMain.add(this.clickData$.subscribe(clickData => {
			if (clickData === undefined) {
				return;
			}

			const lineDataSource = { ...this.lineDataSource };
			const property = AVAILABLE_FILTER_KEYS.timeRange;

			const payload: FilterSetting[] = [{
				property,
				range: undefined
			}];

			if (!lineDataSource?.datasets) {
				return;
			}

			if (clickData) {
				const label = clickData.labelChart;
				const datasetIndex = clickData.datasetIndex;
				lineDataSource.config = buildHighlightLineAnnotation(label, datasetIndex);
				const dateFilter = payload.find(fs => fs.property === property);
				if (dateFilter && label) {
					const date = lineDataSource.labelDate?.length ? lineDataSource.labelDate[datasetIndex] : new Date(label);
					dateFilter.range = {
						from: date,
						to: date
					}
				}
			}
			else {
				lineDataSource.config = buildHighlightLineAnnotation();
			}

			this.lineDataSource = lineDataSource;
			this.filterClient.applyFilter(payload);
		}));
	}

	onClickHandler(event: ClickedData): void {
		const currentClickedData = this.clickDataBehavior.getValue();
		if (currentClickedData?.datasetIndex === event.datasetIndex && currentClickedData.index === event.index) {
			this.clickDataBehavior.next(null);
		} else {
			this.clickDataBehavior.next(event);
		}
	}
}
```

```
User clicks a data point
  └─► onClickHandler(event: ClickedData)
        │  Toggle: if same point → next(null), else → next(event)
        └─► clickDataBehavior (BehaviorSubject)
              └─► clickData$ subscription (wired in ngOnInit)
                    ├── clickData = ClickedData  →  draw highlight line at x-label
                    │                              apply { property: 'date', range: { from, to } }
                    │                              via filterClient.applyFilter([...])
                    └── clickData = null  →  remove highlight line
                                            apply { property: 'date', range: undefined }
                                            (restores dashboard panel date range)
```

Key rules:
- Store time-series dates in `lineDataSource.labelDate[]` so the filter range uses real `Date` objects, not display strings.
- Always call `this.clickDataBehavior.next(undefined)` at the **start** of `applyData` to cancel any pending click state when the data reloads.
- The `config` property on `lineDataSource` is merged into Chart.js options — the base class uses it to inject the annotation plugin highlight line `buildHighlightLineAnnotation()`.
- Use `buildHighlightLineAnnotation(label?, datasetIndex?)` from `network-performance.constants.ts` to generate the annotation config.

---

**Multiple Datasets Chart — Click-to-Highlight Pattern**

For multiple datasets charts use index-based color mutation instead of a line annotation. Add these two helpers to the **domain widget base** class (e.g. `MyDomainWidgetBaseDataComponent`) so every widget in the domain can reuse them:

```typescript
// my-domain-widget-base-data.component.ts
import { ChartColors } from '@sccvision/core';

export abstract class MyDomainWidgetBaseDataComponent extends WidgetBaseDataComponent {

  /**
   * Highlights the bar at `clickedIndex` across all datasets and dims the rest (50% opacity).
   * Pass `null` to reset all catalogues to the chart's default color.
   */
  protected applyHighlightByDatasetIndex(dataSource: any, clickedIndex: number | null): any {
    if (!dataSource?.datasets) return dataSource;
    return {
      ...dataSource,
      datasets: dataSource.datasets.map((dataset: any, dsIdx: number) => {
        if (clickedIndex === null) {
          return { ...dataset, backgroundColor: undefined };  // restore chart default
        }
        const base = ChartColors[dsIdx % ChartColors.length];
        return {
          ...dataset,
          backgroundColor: (dataset.data as any[]).map((_: any, i: number) =>
            i === clickedIndex ? base : base + '80',  // '80' = 50% opacity in hex
          ),
        };
      }),
    };
  }

  /**
   * Highlights selected dataset slice at `clickedIndex` and dims the rest (50% opacity).
   * Pass `null` to restore all slices to their original ChartColors values.
   */
  protected applyHighlightByIndex(dataSource: any, clickedIndex: number | null): any {
    if (!dataSource?.datasets) return dataSource;
    return {
      ...dataSource,
      datasets: dataSource.datasets.map((dataset: any) => ({
        ...dataset,
        backgroundColor: (dataset.data as any[]).map((_: any, i: number) => {
          const base = ChartColors[i % ChartColors.length];
          return clickedIndex === null || i === clickedIndex ? base : base + '80';
        }),
      })),
    };
  }
}
```

**Using the helpers in a concrete chart widget:**

```typescript
// widgets/my-chart/my-chart.component.ts
const SELECTOR = 'my-chart-widget';

@Component({ selector: SELECTOR, providers: [MyDomainService],
             imports: [ChartComponent], templateUrl: './my-chart.component.html' })
export class MyChartComponent extends MyDomainWidgetBaseDataComponent
    implements OnInit, OnDestroy {

  protected chartDataSource: any;
  private clickedIndex: number | null = null;  // tracks which chart element is selected

  constructor() {
    super();
    this.filterClient = useWidgetFilter({ widgetId: SELECTOR });
    this.configuration = { title: 'Open Risks by Owner', subtitle: '', icon: 'bar_chart' };
  }

  override loadData(triggers: Record<string, any>): Observable<any> {
    const { tenantId, body } = triggers;
    if (!tenantId) return of(null);
    return this.myDomainService.getChartData(tenantId, body).pipe(map(r => r?.data));
  }

  override applyData(data: any[]): void {
    this.clickedIndex = null;  // ALWAYS reset on new data load
    if (!data?.length) { this.chartDataSource = null; return; }

    this.chartDataSource = {
      labels: data.map(d => d.owner),
      datasets: [{ label: 'Open Risks', data: data.map(d => d.count) }]
    };
  }

  onClickHandler(event: ClickedData): void {
    const newIndex = event?.dataIndex ?? null;
    // Toggle off if same bar is clicked again
    const next = newIndex === this.clickedIndex ? null : newIndex;
    this.clickedIndex = next;
    this.chartDataSource = this.applyHighlightByIndex(this.chartDataSource, next);

    // Publish filter — pass null values to deselect
    const filterValue = next !== null ? [this.chartDataSource.labels[next]] : [];
    this.filterClient.applyFilter([{ property: 'owner', values: filterValue }]);
  }
}
```

**Template:**
```html
<wgt-chart
  [dataSources]="chartDataSource"
  (clickElementEmitter)="onClickHandler($event)">
</wgt-chart>
```

**How it works:**
```
User clicks bar at index 2
  └─► onClickHandler({ dataIndex: 2, ... })
        ├── same index as before?  → clickedIndex = null  (deselect)
        └── different index?       → clickedIndex = 2

  applyHighlightByIndex(dataSource, 2)
    └─► for each dataset:
          element[2]  → ChartColors[dsIdx]         (full color)
          element[0,1,3,...] → ChartColors[dsIdx] + '80'  (50% opacity)

  filterClient.applyFilter([{ property: 'owner', values: ['Alice'] }])
    → published on ClickedEvent channel
    → dashboard merges with panel filters → rebroadcasts to all widgets

Deselect (click same bar again):
  applyHighlightByIndex(dataSource, null)
    └─► backgroundColor: undefined  →  chart reverts to its default colors
  filterClient.applyFilter([{ property: 'owner', values: [] }])
```

> For **single dataset** charts: replace `applyHighlightByDatasetIndex` → `applyPieHighlightByIndex` with the same `(clickElementEmitter)` binding. The only difference is by **slice index** (not dataset index) since a single dataset chart has one dataset with multiple colors.

---

### 7. Register Widgets

```typescript
// widgets/index.ts
export const myDomainWidgets = [
  { name: 'my-stat-widget',       component: MyStatComponent },
  { name: 'my-line-chart-widget', component: MyLineChartComponent },
];
```

```typescript
// my-domain.module.ts  (or the parent NgModule)
@NgModule({
  imports: [CommonModule, RouterModule.forChild(routes)],
  providers: [{ provide: WIDGET_TOKEN, useValue: myDomainWidgets }]
})
export class MyDomainModule {}
```

---

## Key Imports Reference

| Symbol | Package |
|--------|---------|
| `useDashboardFilter` | `@sccvision/core` |
| `useWidgetFilter` | `@sccvision/core` |
| `formatDataSource` | `@sccvision/core` |
| `buildHighlightLineAnnotation` | `@sccvision/core` |
| `Filter`, `FilterType`, `DateRangeFilter`, `MultiSelectFilter`, `FilterOption` | `@sccvision/core` |
| `isMultiSelectFilter`, `isInputFilter` | `@sccvision/core` |
| `FilterSetting` | `@sccvision/common` |
| `CommunicationService` | `@sccvision/common` |
| `StateManager`, `AppStateNames`, `SharedEventName` | `@sccvision/common` |
| `WIDGET_TOKEN` | `@sccvision/common` |
| `WidgetContainerComponent`, `WidgetBaseComponent`, `WidgetChartComponent` | `@sccvision/widgets` |
| `StatWidgetComponent`, `LineChartComponent`, `BarChartComponent` | `@sccvision/widgets` |
| `WidgetConfig`, `SubtitleType`, `ClickedData` | `@sccvision/widgets` |

---

## Filter Type Reference

All filter interfaces live in `@sccvision/core` (`filter.constants.ts`). Every filter shares the base `Filter` interface:

```typescript
interface Filter {
  key: string;       // Unique key — also used as FilterSetting.property in broadcasts
  label: string;     // Display label shown in the filter panel
  type: FilterType;  // Discriminant used to narrow the interface
  isHidden?: boolean;
  isPinned?: boolean;
}
```

---

### `FilterType.DateRange` → `DateRangeFilter`

Date picker with a start and end date.

```typescript
interface DateRangeFilter extends Filter {
  type: FilterType.DateRange;
  dateFrom: Date;          // Current start date (required)
  dateTo: Date;            // Current end date (required)
  defaultDateFrom?: Date;  // Reset-to value for start date
  defaultDateTo?: Date;    // Reset-to value for end date
}
```

**Usage in `useDashboardFilter.init()`:**
```typescript
{
  key: 'date',
  label: 'Date Range',
  type: FilterType.DateRange,
  dateFrom: new Date('2026-01-01'),
  dateTo:   new Date()
}
```

**In `buildRequestBody` — always use `.toFormattedString()`:**
```typescript
const dateFilter = filterSettings.find(fs => fs.property === 'date');
if (dateFilter?.range?.from instanceof Date) {
  body.startDate = dateFilter.range.from.toFormattedString(); // 'yyyy-MM-dd'
  body.endDate   = dateFilter.range.to.toFormattedString();
}
```

> ⚠️ Never use `.toISOString()` — it shifts the date to UTC and causes off-by-one bugs in UTC+ timezones.

---

### `FilterType.MultiSelect` → `MultiSelectFilter`

Checkbox list; supports select-all and optional grouped rendering.

```typescript
interface MultiSelectFilter extends Filter {
  type: FilterType.MultiSelect;
  data: FilterOption[];                  // Available options
  isAllowedToSelectAll?: boolean;        // Show "Select All" checkbox
  groupOptionsBuilder?: (options: FilterOption[]) =>
    { label: string; options: ModifiedFilterOption[]; isOptionsHidden?: boolean }[];
    // Provide to render options inside <mat-optgroup>. Not auto-grouped by default.
}

interface FilterOption {
  key: string;       // Value sent in FilterSetting.values[]
  label: string;     // Display text
  checked?: boolean; // Pre-selected state
}
```

**Usage:**
```typescript
{
  key: 'status',
  label: 'Status',
  type: FilterType.MultiSelect,
  isAllowedToSelectAll: true,
  data: [
    { key: 'open',   label: 'Open' },
    { key: 'closed', label: 'Closed' }
  ]
}
```

**In `buildRequestBody`:**
```typescript
const statusFilter = filterSettings.find(fs => fs.property === 'status');
if (statusFilter?.values?.length) {
  body.statuses = statusFilter.values as string[];
}
```

**Grouped options example:**
```typescript
groupOptionsBuilder: (options) => [
  { label: 'Active',   options: options.filter(o => ['open','in-progress'].includes(o.key)) },
  { label: 'Resolved', options: options.filter(o => ['closed','cancelled'].includes(o.key)) }
]
```

---

### `FilterType.SingleSelect` → `SingleSelectFilter`

Dropdown with one selectable value.

```typescript
interface SingleSelectFilter extends Filter {
  type: FilterType.SingleSelect;
  data: FilterOption[];
}
```

**Usage:**
```typescript
{
  key: 'owner',
  label: 'Owner',
  type: FilterType.SingleSelect,
  data: owners.map(o => ({ key: o.id, label: o.name }))
}
```

**In `buildRequestBody`:**
```typescript
const ownerFilter = filterSettings.find(fs => fs.property === 'owner');
if (ownerFilter?.values?.[0]) {
  body.ownerId = ownerFilter.values[0] as string;
}
```

---

### `FilterType.Input` → `InputFilter`

Free-text input field.

```typescript
interface InputFilter extends Filter {
  type: FilterType.Input;
  placeholder?: string;
  defaultValue?: string;
}
```

**Usage:**
```typescript
{
  key: 'search',
  label: 'Search',
  type: FilterType.Input,
  placeholder: 'Enter name...',
  defaultValue: ''
}
```

**In `buildRequestBody`:**
```typescript
const searchFilter = filterSettings.find(fs => fs.property === 'search');
if (searchFilter?.values?.[0]) {
  body.search = searchFilter.values[0] as string;
}
```

---

### `FilterType.ToggleButton` → `ToggleButtonFilter`

Binary or multi-state toggle (e.g. On / Off, Day / Week / Month).

```typescript
interface ToggleButtonFilter extends Filter {
  type: FilterType.ToggleButton;
  labels: string[];   // Button labels (e.g. ['Day', 'Week', 'Month'])
  toggled?: boolean;  // Current active state (for binary toggles)
}
```

**Usage:**
```typescript
{
  key: 'granularity',
  label: 'Granularity',
  type: FilterType.ToggleButton,
  labels: ['Day', 'Week', 'Month']
}
```

**In `buildRequestBody`:**
```typescript
const granFilter = filterSettings.find(fs => fs.property === 'granularity');
if (granFilter?.values?.[0]) {
  body.granularity = granFilter.values[0] as string;
}
```

---

### Type Guards (import from `@sccvision/core`)

Use these to safely narrow `Filter` to a specific subtype:

```typescript
import {
  isDateRangeFilter,
  isMultiSelectFilter,
  isSingleSelectFilter,
  isInputFilter,
  isToggleButtonFilter
} from '@sccvision/core';

filters.forEach(filter => {
  if (isDateRangeFilter(filter))    { /* filter.dateFrom / filter.dateTo */ }
  if (isMultiSelectFilter(filter))  { /* filter.data, filter.isAllowedToSelectAll */ }
  if (isSingleSelectFilter(filter)) { /* filter.data */ }
  if (isInputFilter(filter))        { /* filter.placeholder */ }
  if (isToggleButtonFilter(filter)) { /* filter.labels, filter.toggled */ }
});
```

---

### `FilterSetting` — broadcast payload on channels

```typescript
interface FilterSetting {
  property: string;                             // matches Filter.key
  values?: any[];                               // multi-select / single-select / input / toggle
  range?: { from: Date | number; to: Date | number }; // date-range
}
```

---

## WidgetBaseDataComponent — Detailed Guide

### Class Hierarchy

```
WidgetBaseComponent           (@sccvision/widgets)
  └── WidgetBaseDataComponent  (src/app/shared/widget-base-data.component.ts)
        └── MyDomainWidgetBaseDataComponent  (domain-specific abstract)
              └── MyStatComponent, MyLineChartComponent, …  (concrete widgets)
```

### Inherited from `WidgetBaseComponent`

| Member | Type | Description |
|--------|------|-------------|
| `$configuration` | `Observable<WidgetConfig>` | Observable of the widget header config (title, subtitle, icon) |
| `configuration` (setter) | `WidgetConfig` | Set in the subclass constructor to populate the widget header |
| `dataLoaded` | `EventEmitter<boolean>` | Emits `false` on load-start and `true` on load-end; wired by `WidgetContainerComponent` to show/hide the progress bar |
| `moreOptionClicked` | `EventEmitter<MouseEvent\|KeyboardEvent>` | Emits when the more-options button is clicked |
| `formatNumber(value, fixed?)` | `string` | Formats large numbers with K/M/B/T suffixes |
| `hexToRgba(hex, alpha?)` | `string` | Converts a hex color to an `rgba()` string |

### Members of `WidgetBaseDataComponent`

| Member | Type | Description |
|--------|------|-------------|
| `subMain` | `Subscription` | Composite subscription; all long-lived subs added here; unsubscribed in `ngOnDestroy` |
| `requestBodySubject` | `BehaviorSubject<any>` | Holds the latest request body built from filter settings |
| `requestBodyObservable$` | `Observable<any>` | Public read-only view of `requestBodySubject`; used as a trigger source |
| `filterClient` | `ReturnType<typeof useWidgetFilter>` | Assign in the subclass constructor via `useWidgetFilter({ widgetId: SELECTOR })` |

### Abstract / Required Overrides

```typescript
// 1. Define what observables trigger a data reload
abstract getTriggerSources(): Record<string, Observable<any>>;

// 2. Call the API; receives a resolved snapshot of all trigger values
abstract loadData(triggers: Record<string, any>): Observable<any>;

// 3. Apply the API response to the component's template-facing properties
abstract applyData(data: any): void;
```

### Optional Override

```typescript
// Transform FilterSetting[] → domain request body.
// Called by ngOnInit whenever the filter channel emits.
// Return null/undefined to skip updating the requestBodySubject.
protected buildRequestBody?(filterSettings: FilterSetting[]): any;
```

### `ngOnInit` Execution Order

```
ngOnInit()
  │
  ├─► filterClient.init(onFilterApply)
  │     │
  │     └─► On each FilterSetting[] broadcast:
  │           if (buildRequestBody) → requestBodySubject.next(body)
  │
  └─► setupDataLoadingOrchestrator()
        │
        └─► combineLatest(Object.values(getTriggerSources()))
              │
              └─► switchMap(triggers → loadData(triggers))
                    ├─► dataLoaded.emit(false)   ← progress bar on
                    ├─► API call
                    ├─► catchError → of(null)
                    ├─► finalize → dataLoaded.emit(true)   ← progress bar off
                    └─► subscribe(response → applyData(response))
```

### Constructor Pattern

```typescript
const SELECTOR = 'my-widget';   // must match widget registration name

export class MyWidgetComponent extends MyDomainWidgetBaseDataComponent {
  dataSource: any;

  constructor() {
    super();
    // 1. Assign filterClient BEFORE ngOnInit runs
    this.filterClient = useWidgetFilter({ widgetId: SELECTOR });
    // 2. Set widget header configuration
    this.configuration = {
      title: 'Widget Title',
      subtitle: 'unit label',
      subtitleType: SubtitleType.success,  // success | error | warning | info | default | none
      icon: 'material-icon-name',
      moreOption: false                    // show three-dot menu button
    };
  }
}
```

### `getTriggerSources()` — Common Patterns

```typescript
// Pattern 1: Filter-driven only (body emits when filter changes)
override getTriggerSources(): Record<string, Observable<any>> {
  return { body: this.requestBodyObservable$ };
}

// Pattern 2: Tenant + filter (standard network-performance pattern)
override getTriggerSources(): Record<string, Observable<any>> {
  return {
    tenantId: this.state.getStatebyName(AppStateNames.Customer).pipe(
      debounceTime(150),
      distinctUntilChanged((a: any, b: any) => a?.tenantId === b?.tenantId),
      map(s => s?.tenantId),
      tap(() => this.requestBodySubject.next({ ...this.requestBodySubject.value, resourceNames: [] }))
    ),
    body: this.requestBodyObservable$
  };
}

// Pattern 3: Static (no filter, always loads on init — e.g. summary cards)
override getTriggerSources(): Record<string, Observable<any>> {
  return { trigger: of(true) };
}
```

### `loadData()` — Guards and Error Handling

```typescript
override loadData(triggers: Record<string, any>): Observable<any> {
  const { tenantId, body } = triggers;

  // Always guard for missing tenantId — the trigger emits before state resolves
  if (!tenantId) return of(null);

  return this.myService.getData(tenantId, body).pipe(
    map(response => response?.data),   // unwrap envelope
    catchError(() => of(null))         // prevent orchestrator from crashing
  );
}
```

### `applyData()` — Null Safety

```typescript
override applyData(data: MyItem[]): void {
  // Always guard for null/empty — catchError above can return null
  if (!data?.length) {
    this.dataSource = {};
    return;
  }
  // Compute and assign to template-facing properties
  this.dataSource = { value: this.formatNumber(data[0].metric) };
}
```

---

## Widget Type Reference

All chart widgets extend `WidgetChartComponent` and accept:

| Input | Type | Description |
|-------|------|-------------|
| `[dataSources]` | `any` | Chart data object (shape varies by widget — see below) |
| `[settings]` | `WidgetConfig \| undefined` | Widget header config; bind to `$configuration \| async` |
| `(clickElementEmitter)` | `EventEmitter<ClickedData>` | Emits on element click (bar, point, slice) |
| `(chartEvent)` | `EventEmitter<ChartEventPayload>` | Emits label-click or tooltip events |

Colors auto-cycle through `ChartColors[]` from `@sccvision/core` when not specified.

---

### `StatWidgetComponent` — `<wgt-stat-chart>`

KPI tile showing a primary value with optional percentage change badge.

```typescript
import { StatWidgetComponent } from '@sccvision/widgets';
```

```html
<wgt-stat-chart [dataSources]="dataSource" [settings]="$configuration | async"></wgt-stat-chart>
```

**`dataSources` shape:**

```typescript
interface StatDataSource {
  value: number | string;     // Required — the main KPI value
  label?: string;             // Optional secondary label below title
  icon?: string;              // Optional Material icon name shown beside label
  percent?: number;           // Optional change ratio (e.g. 0.12 = +12%) — shows trending arrow
  percentColor?: string;      // Optional hex color for percent badge (overrides success/error class)
  color?: string;             // Optional hex color for the value text
  currency?: string;          // Optional ISO currency code (e.g. 'USD') — formats as currency
  fixedNumber?: number;       // Optional decimal places override
}
```

---

### `LineChartComponent` — `<wgt-line-chart>`

Time-series or category line chart with optional gradient fill and click-to-filter support.

```typescript
import { LineChartComponent } from '@sccvision/widgets';
```

```html
<wgt-line-chart [dataSources]="dataSources" [settings]="$configuration | async"
                (clickElementEmitter)="onChartClick($event)"></wgt-line-chart>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];              // x-axis labels
  datasets: Array<{
    label: string;
    data: number[];
    borderColor?: string[];      // line color; auto-assigned from ChartColors if omitted
    backgroundColor?: string[];  // fill color or gradient base
    linearGradient?: boolean;    // true → gradient fill from backgroundColor[0] to transparent
    fill?: boolean;              // fill area below line (default false)
    tension?: number;            // curve tension (default 0.1)
    // Any other Chart.js dataset option
  }>;
  aspectRatio?: number;          // default 2
  options?: any;                 // merged into Chart.js options (e.g. annotation plugin config)
}
```

---

### `formatDataSource` — Multi-Series Chart Data Helper

`formatDataSource` is a utility from `@sccvision/core` that transforms a **flat API array** into the multi-series `{ labels, datasets }` shape required by line, bar, and other chart widgets.

```typescript
import { formatDataSource } from '@sccvision/core';
```

**Signature:**

```typescript
function formatDataSource<T>(
  data: T[],
  groupBy: keyof T,                      // field used as x-axis / label (e.g. 'date', 'category')
  nameExtractor: (item: T) => string,    // returns the series/dataset name (e.g. item.resourceShortName)
  valueExtractor: (item: T) => number,   // returns the numeric value to aggregate
  aggregation?: 'sum' | 'average' | 'top' | 'bottom',  // default 'sum'
  syntaxDate?: string,                   // date format for groupBy values (e.g. 'dd/MM/yyyy')
): {
  labels: string[];                      // sorted x-axis labels
  dates: Date[];                         // Date objects matching labels (only when syntaxDate is set)
  mappingData: Record<string, Record<string, number>>;  // [seriesName][label] → aggregated value
}
```

**How it works:**
1. Groups each item by `item[groupBy]` to form x-axis labels.
2. Uses `nameExtractor` to determine which dataset/series the item belongs to.
3. Applies `aggregation` (`sum` / `average` / `top` / `bottom`) when multiple items share the same series + label.
4. When `syntaxDate` is provided: parses the `groupBy` value as a `Date`, formats it with `toFormattedString(syntaxDate)`, and sorts labels chronologically. The raw `Date` objects are returned in `dates[]` for use as filter range values.
5. Returns a `mappingData` record — use it to build each dataset's `data[]` by mapping over `labels`.

**Example — multi-series line chart (one series per resource, grouped by date):**

```typescript
// API response shape: [{ date: '2026-01-15', resourceShortName: 'Router-A', responseTime: 42 }, ...]
import { formatDataSource, ChartColors } from '@sccvision/core';

override applyData(data: NetworkPerformanceItem[]): void {
  this.clickDataBehavior.next(undefined); // reset click state on reload

  if (!data?.length) {
    this.lineDataSource = {};
    return;
  }

  const dataMapping = formatDataSource<NetworkPerformanceItem>(
    data,
    'date',                                              // group x-axis by date
    item => item.resourceShortName || 'Unknown',         // one series per resource
    item => item.responseTime ?? 0,                      // y-axis value
    'sum',                                               // aggregation (or 'max', 'average', etc.)
    'dd/MM/yyyy'                                         // format dates; enables chronological sort + Date objects
  );

  this.lineDataSource = {
    labels: dataMapping.labels,              // formatted x-axis labels, sorted chronologically
    labelDate: dataMapping.dates,            // real Date objects → used by click-to-date-filter
    datasets: Object.keys(dataMapping.mappingData).map((resource, i) => ({
      label: resource,
      data: dataMapping.labels.map(label => dataMapping.mappingData[resource]?.[label] ?? 0),
      backgroundColor: ChartColors[i % ChartColors.length],
      borderColor: ChartColors[i % ChartColors.length]
    })),
    displayLegend: false
  };
}
```

**Aggregation modes:**

| Mode | Use case | Formula |
|------|----------|---------|
| `'sum'` | Totals (throughput, count) | `acc + value` |
| `'average'` | Means (latency, utilisation %) | Running average |
| `'top'` | Peak values (max ping, max bandwidth) | `Math.max(acc, value)` |
| `'bottom'` | Minimum values (min response time) | `Math.min(acc, value)` |

**Key rules:**
- Always provide `syntaxDate` when `groupBy` is a date field — this enables chronological sorting and populates `dates[]`.
- Store `dataMapping.dates` in `lineDataSource.labelDate` so the click-to-date-filter in `NetworkPerformanceLineWidgetDataComponent` receives real `Date` objects, not display strings.
- For missing series/label combinations, `dataMapping.mappingData[series]?.[label]` is `undefined` — fall back to `0` (or `null` to produce a gap in the line).
- `formatDataSource` does **not** produce Chart.js dataset objects — you must map `mappingData` into `datasets[]` manually (see example above).

**Aggregation must match the API `aggregateType`:**
> If the API already aggregates with `max`, pass `aggregation: 'top'` (or `'sum'` when the API returns one row per series+date). Applying `'sum'` on pre-aggregated `max` data will produce wrong results if multiple rows share the same series+label.

---

### `BarChartComponent` — `<wgt-bar-chart>`

Stacked or grouped bar/column chart. Supports horizontal bars via `indexAxis: 'y'`.

```typescript
import { BarChartComponent } from '@sccvision/widgets';
```

```html
<wgt-bar-chart [dataSources]="dataSources" [settings]="$configuration | async"
               (clickElementEmitter)="onChartClick($event)"></wgt-bar-chart>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];
  datasets: Array<{
    label: string;
    data: number[];
    backgroundColor?: string[];
    borderColor?: string[];
    borderWidth?: number;       // default 0
    // Any Chart.js dataset option
  }>;
  indexAxis?: 'x' | 'y';       // 'y' for horizontal bars (default 'x')
  stacked?: boolean;            // default true
  aspectRatio?: number;         // default 2
}
```

---

### `PieWidgetComponent` — `<wgt-pie-chart>`

Pie or partial-arc chart. Set `circumference` < 360 for a semi-circle.

```typescript
import { PieWidgetComponent } from '@sccvision/widgets';
```

```html
<wgt-pie-chart [dataSources]="dataSources" [settings]="$configuration | async"
               (clickElementEmitter)="onChartClick($event)"></wgt-pie-chart>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];
  datasets: Array<{
    data: number[];
    backgroundColor: string[];   // one color per slice
  }>;
  circumference?: number;        // arc degrees (default 360 = full circle)
  aspectRatio?: number;          // default 1
  displayLegend?: boolean;       // default true
}
```

> **Note:** Implements `NotAllowAllZeroData` — if all data values are 0, the chart renders the default "No data" placeholder instead of crashing.

---

### `GaugeWidgetComponent` — `<wgt-gauge-chart>`

Doughnut-style gauge with center text annotation. Typically used for a single percentage value.

```typescript
import { GaugeWidgetComponent } from '@sccvision/widgets';
```

```html
<wgt-gauge-chart [dataSources]="dataSources" [settings]="$configuration | async"></wgt-gauge-chart>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];
  gaugeDataset: string[];       // Text lines rendered in the center (e.g. ['85%', 'Utilization'])
  datasets: Array<{
    data: number[];             // Segment values (e.g. [85, 15] for filled + remainder)
    backgroundColor: string[];  // One color per segment
  }>;
  circumference?: number;       // Arc degrees (default 180 = half-circle)
  aspectRatio?: number;         // default uses calculated value
}
```

---

### `GaugeNeedleWidgetComponent` — `<wgt-gauge-needle-chart>`

Gauge with a needle pointer. Needle position is driven by `options.plugins.needle`.

```typescript
import { GaugeNeedleWidgetComponent } from '@sccvision/widgets';
```

```html
<wgt-gauge-needle-chart [dataSources]="dataSources" [settings]="$configuration | async"></wgt-gauge-needle-chart>
```

**`dataSources` shape:**

```typescript
{
  labels?: string[];
  gaugeDataset?: string[];        // Optional center text annotation
  datasets: Array<{
    data: number[];               // Segment sizes (e.g. colored bands)
    backgroundColor: string[];
  }>;
  circumference?: number;         // default 280
  rotation?: number;              // start angle in degrees (default -140)
  cutout?: string;                // inner radius (default '90%')
  aspectRatio?: number;           // default 1.25
  options: {
    plugins: {
      needle: {
        value: number;            // Current needle position
        min?: number;             // Scale minimum (default 0)
        max?: number;             // Scale maximum (default 100)
        color?: string;           // Needle color
        width?: number;           // Needle line width
        knobRadius?: number;      // Center knob radius
        knobColor?: string;       // Center knob color
      }
    }
  }
}
```

---

### `AnalyticsGaugeComponent` — `<wgt-analytics-gauge-chart>`

Advanced doughnut gauge with custom legend and multi-line center annotation. Suitable for score/analytics dashboards.

```typescript
import { AnalyticsGaugeComponent } from '@sccvision/widgets';
```

```html
<wgt-analytics-gauge-chart [dataSources]="dataSources" [settings]="$configuration | async"></wgt-analytics-gauge-chart>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];              // Slice labels (shown in custom legend)
  datasets: Array<{
    data: number[];
    backgroundColor: string[];
    label?: string;
  }>;
  gaugeDataset?: string[];       // Center annotation lines; if omitted, auto-calculates from data
  scale?: number;                // Max range multiplier (default 0.8 × max value)
  flexLabel?: boolean;           // Flexible label rendering (default false)
}
```

---

### `RadarChartComponent` — `<wgt-radar-chart>`

Radar/spider chart for multi-axis comparisons.

```typescript
import { RadarChartComponent } from '@sccvision/widgets';
```

```html
<wgt-radar-chart [dataSources]="dataSources" [settings]="$configuration | async"></wgt-radar-chart>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];              // Axis labels
  datasets: Array<{
    label: string;
    data: number[];              // One value per axis
    backgroundColor?: string[];
    borderColor?: string[];
    tension?: number;            // default 0
    borderWidth?: number;        // default 1
  }>;
  aspectRatio?: number;          // default 1.25
}
```

---

### `ScoreGapRadarChartComponent` — `<wgt-score-gap-radar-chart>`

Radar variant for score-gap analysis. Points shown without connecting lines by default (`showLine: false`).

```typescript
import { ScoreGapRadarChartComponent } from '@sccvision/widgets';
// Also import RadarKind enum if needed:
import { RadarKind } from '@sccvision/widgets';
// RadarKind = { Current, Target, Uncovered, Generic }
```

```html
<wgt-score-gap-radar-chart [dataSources]="dataSources" [settings]="$configuration | async"></wgt-score-gap-radar-chart>
```

**`dataSources` shape:** Same as `RadarChartComponent`. Use `RadarKind` values in `dataset.label` for semantic coloring.

---

### `MultiSeriesChartComponent` — `<wgt-multi-series-chart>`

Combines bar and line series in a single chart. Each dataset declares its own `type`.

```typescript
import { MultiSeriesChartComponent } from '@sccvision/widgets';
```

```html
<wgt-multi-series-chart [dataSources]="dataSources" [settings]="$configuration | async"
                        (clickElementEmitter)="onChartClick($event)"></wgt-multi-series-chart>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];
  datasets: Array<{
    type: 'bar' | 'line';         // Required per-dataset
    label: string;
    data: number[];
    backgroundColor?: string[];
    borderColor?: string[];
    tension?: number;             // line only (default 0.1)
    borderWidth?: number;         // default 2
  }>;
  indexAxis?: 'x' | 'y';         // default 'x'
  aspectRatio?: number;           // default 2
}
```

---

### `SquareWidgetComponent` — `<wgt-square-widget>`

Grid of colored square buttons — one per label. Useful for status dashboards or category breakdowns. Each square is clickable and emits via `clickElementEmitter`.

```typescript
import { SquareWidgetComponent } from '@sccvision/widgets';
```

```html
<wgt-square-widget [dataSources]="dataSources" [settings]="$configuration | async"
                   (clickElementEmitter)="onSquareClick($event)"></wgt-square-widget>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];               // One label per square cell
  datasets: Array<{               // Single dataset (only datasets[0] is used)
    data: (number | string)[];    // Value shown inside each square
    color?: string[];             // Text color per cell
    backgroundColor?: string[];   // Background color per cell (default ChartColors cycling)
  }>;
}
```

---

### `PercentageBreakdownWidgetComponent` — `<wgt-percentage-breakdown-widget>`

Horizontal stacked percentage bar. Each dataset item is a named segment shown as a proportion of the total.

```typescript
import { PercentageBreakdownWidgetComponent } from '@sccvision/widgets';
```

```html
<wgt-percentage-breakdown-widget [dataSources]="dataSources" [settings]="$configuration | async"
                                 (clickElementEmitter)="onSegmentClick($event)"></wgt-percentage-breakdown-widget>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];               // Segment names
  datasets: Array<{               // Single dataset
    data: number[];               // Segment values
    total?: number;               // Override denominator; defaults to sum of data
    color?: string[];             // Text color per segment
    backgroundColor?: string[];   // Bar color per segment
  }>;
  displayPercentage?: boolean;    // Show percentage labels (default true)
}
```

---

### `MapChartComponent` — `<wgt-map-chart>`

World-map bubble chart. Each data point is plotted at its geographic coordinates. Hovering shows a donut tooltip.

```typescript
import { MapChartComponent } from '@sccvision/widgets';
import type { MapChartData } from '@sccvision/widgets';
```

```html
<wgt-map-chart [dataSources]="dataSources" [settings]="$configuration | async"></wgt-map-chart>
```

**`dataSources` shape:**

```typescript
{
  labels: string[];
  datasets: Array<{
    data: MapChartData[];        // Geographic data points
    // Other Chart.js geo dataset options (borderColor, outlineBackgroundColor, etc.)
  }>;
}

interface MapChartData {
  name: string;      // Location name (tooltip label)
  latitude: number;
  longitude: number;
  value: number;     // Bubble size / tooltip value
}
```

---

## Communication Channel Naming

Default channels (no topic override):
- `FilterApply` — filter settings broadcast
- `ClickedEvent` — widget click-filter publish

Custom topic (`setting.topic = 'MyFeature'`):
- `MyFeature` — filter settings
- `MyFeature_ClickedEvent` — click filters

Use custom topics only when two independent dashboards are mounted simultaneously and must not cross-pollinate filters.

---

## Common Pitfalls

1. **Forgetting `providers: [MyDomainService]` on the widget component** — each widget needs the service in its own injector if it's not provided at a higher scope.
2. **Not guarding `if (!tenantId) return of(null)` in `loadData()`** — the `tenantId` trigger emits before the customer state resolves.
3. **Mutating `Filter` objects directly in `patchFilter`** — always spread (`{ ...f, data: newData }`) to avoid undetected reference equality.
4. **Using the same `widgetId` string for two widgets** — the self-publish guard uses this to skip own messages; duplicates cause one widget to never react to its own click-filter.
5. **Not resetting stale filter fields on tenant change** — copy the `tap()` pattern in `getTriggerSources()` to clear resource/interface names when tenantId changes.
6. **Calling `filterClient.init()` outside `ngOnInit`** — the hook uses `inject()` for `DestroyRef`; it must be called in or before `ngOnInit`.
7. **Sending raw `Date` objects or `toISOString()` to the API** — always use `.toFormattedString()` (no argument) to produce a `yyyy-MM-dd` string. `toISOString()` converts to UTC which shifts the date by the local timezone offset (e.g. `2026-05-20T17:00:00Z` becomes `2026-05-19` in UTC+7), causing off-by-one date bugs in API requests.

   ```typescript
   // ✅ Correct — timezone-safe
   body.startDate = timeRangeFilter.range.from.toFormattedString(); // 'yyyy-MM-dd'
   body.endDate   = timeRangeFilter.range.to.toFormattedString();

   // ❌ Wrong — shifts date in UTC+7 and later timezones
   body.startDate = timeRangeFilter.range.from.toISOString().slice(0, 10);
   body.endDate   = timeRangeFilter.range.to.toISOString().slice(0, 10);
   ```

   `toFormattedString()` uses `getFullYear() / getMonth() / getDate()` (local time), so the date the user selected is always preserved exactly.

---