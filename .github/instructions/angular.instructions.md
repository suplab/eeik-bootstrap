---
applyTo: "**/*.ts, **/*.html, **/*.scss"
---

## Context

This instruction file applies to Angular frontend code. The project uses Angular 15+ with standalone components as the default architecture (NgModule is only used for legacy modules). New code targets Angular 16+ and should leverage the Signals API (`signal()`, `computed()`, `effect()`) for reactive state management. Components use `OnPush` change detection throughout. The design system uses SCSS with BEM naming conventions.

---

## Coding Standards

- **Standalone components:** All new components, directives, and pipes must use `standalone: true` — no NgModule declarations unless integrating with a legacy module
- **Signals API:** Prefer `signal()`, `computed()`, and `effect()` over RxJS `BehaviorSubject` for local component state in Angular 16+
- **`inject()` function:** Use `inject()` for service injection in standalone components — not constructor parameter injection
- **Typed HTTP:** Always provide a type parameter to `HttpClient` methods: `http.get<Customer[]>('/api/customers')`
- **Async pipe:** Use the `async` pipe in templates for Observables — never manually subscribe and unsubscribe in the component class unless the subscription has side effects that must be explicitly torn down
- **`OnPush` strategy:** All components must declare `changeDetection: ChangeDetectionStrategy.OnPush`
- **Reactive Forms:** Use `ReactiveFormsModule` and `FormBuilder` for any form with more than two fields or validation logic
- **Lazy loading:** All feature routes must use `loadComponent()` (standalone) or `loadChildren()` (module-based) — no eagerly loaded feature routes
- **No `any` type:** TypeScript `any` is forbidden — use `unknown` if the type is genuinely unknown, then narrow it
- **No direct DOM:** Never use `ElementRef.nativeElement` for DOM manipulation — use Angular directives and renderer
- **Accessibility:** All interactive elements (`button`, `input`, `select`, links) must have meaningful `aria-*` attributes or accessible labels
- **No inline styles:** Never bind `[style]` or `[ngStyle]` inline — use CSS classes and `[ngClass]`

---

## Preferred Patterns

### Standalone Component

```typescript
import { Component, ChangeDetectionStrategy, inject, signal, computed } from '@angular/core';
import { CommonModule } from '@angular/common';
import { CustomerService } from '../services/customer.service';
import { Customer } from '../models/customer.model';

@Component({
  selector: 'app-customer-list',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './customer-list.component.html',
  styleUrl: './customer-list.component.scss',
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class CustomerListComponent {
  private readonly customerService = inject(CustomerService);

  readonly customers = signal<Customer[]>([]);
  readonly isLoading = signal(false);
  readonly activeCount = computed(() => this.customers().filter(c => c.active).length);

  ngOnInit(): void {
    this.isLoading.set(true);
    this.customerService.getAll().subscribe({
      next: (data) => this.customers.set(data),
      error: (err) => console.error('Failed to load customers', err),
      complete: () => this.isLoading.set(false),
    });
  }
}
```

### Angular HttpClient Service

```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Customer, CreateCustomerRequest } from '../models/customer.model';

@Injectable({ providedIn: 'root' })
export class CustomerService {
  private readonly http = inject(HttpClient);
  private readonly baseUrl = '/api/v1/customers';

  getAll(activeOnly?: boolean): Observable<Customer[]> {
    const params = activeOnly ? new HttpParams().set('active', 'true') : undefined;
    return this.http.get<Customer[]>(this.baseUrl, { params });
  }

  getById(id: string): Observable<Customer> {
    return this.http.get<Customer>(`${this.baseUrl}/${id}`);
  }

  create(request: CreateCustomerRequest): Observable<Customer> {
    return this.http.post<Customer>(this.baseUrl, request);
  }

  update(id: string, request: Partial<CreateCustomerRequest>): Observable<Customer> {
    return this.http.put<Customer>(`${this.baseUrl}/${id}`, request);
  }
}
```

### Reactive Form

```typescript
import { Component, ChangeDetectionStrategy, inject } from '@angular/core';
import { ReactiveFormsModule, FormBuilder, Validators } from '@angular/forms';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-customer-form',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule],
  templateUrl: './customer-form.component.html',
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class CustomerFormComponent {
  private readonly fb = inject(FormBuilder);

  readonly form = this.fb.group({
    firstName: ['', [Validators.required, Validators.maxLength(100)]],
    lastName: ['', [Validators.required, Validators.maxLength(100)]],
    email: ['', [Validators.required, Validators.email]],
  });

  onSubmit(): void {
    if (this.form.invalid) return;
    // handle valid form
  }
}
```

### Lazy Loading Route

```typescript
// app.routes.ts
export const APP_ROUTES: Routes = [
  {
    path: 'customers',
    loadComponent: () =>
      import('./features/customers/customer-list.component').then(m => m.CustomerListComponent),
  },
  {
    path: 'orders',
    loadChildren: () =>
      import('./features/orders/orders.routes').then(m => m.ORDER_ROUTES),
  },
];
```

### NgRx Store (when shared state is needed)

```typescript
// State defined with createFeature — colocated in one file
export const customerFeature = createFeature({
  name: 'customers',
  reducer: createReducer(
    initialState,
    on(CustomerActions.loadCustomers, state => ({ ...state, loading: true })),
    on(CustomerActions.loadCustomersSuccess, (state, { customers }) =>
      ({ ...state, customers, loading: false })),
  ),
});
```

### Template: `*ngFor` with `trackBy`

```html
<!-- CORRECT: always use trackBy to prevent unnecessary DOM re-renders -->
<ul>
  @for (customer of customers(); track customer.id) {
    <li class="customer-list__item">{{ customer.firstName }} {{ customer.lastName }}</li>
  }
</ul>
```

### SCSS — BEM Naming

```scss
// customer-list.component.scss
.customer-list {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-md);

  &__item {
    padding: var(--spacing-sm);
    border-bottom: 1px solid var(--color-border);
  }

  &__item--active {
    font-weight: 600;
    color: var(--color-primary);
  }

  &__empty-state {
    text-align: center;
    color: var(--color-text-muted);
  }
}
```

---

## Anti-Patterns — Do NOT Generate

```typescript
// WRONG: no type on HttpClient call
this.http.get('/api/customers');

// WRONG: any type
const data: any = response;

// WRONG: manual subscription without takeUntil / takeUntilDestroyed
this.customerService.getAll().subscribe(data => this.customers = data);
// use async pipe in template instead, or takeUntilDestroyed(this.destroyRef)

// WRONG: constructor injection in standalone component (use inject() instead)
constructor(private customerService: CustomerService) {}

// WRONG: ElementRef DOM manipulation
this.el.nativeElement.style.display = 'none';

// WRONG: Default change detection strategy
@Component({ selector: 'app-foo', template: '' })
// Missing changeDetection: ChangeDetectionStrategy.OnPush

// WRONG: eager route loading
{ path: 'orders', component: OrderListComponent }

// WRONG: NgModule for new standalone features
@NgModule({ declarations: [MyNewComponent], ... })
```

```html
<!-- WRONG: inline styles -->
<div [style.color]="'red'">text</div>

<!-- WRONG: no trackBy / track in ngFor -->
<li *ngFor="let item of items">{{ item.name }}</li>

<!-- WRONG: no aria label on icon-only button -->
<button (click)="delete(item)"><mat-icon>delete</mat-icon></button>
```

---

## Dependencies & Versions

| Library | Version | Notes |
|---------|---------|-------|
| Angular | 15+ (16+ for Signals) | `@angular/core`, `@angular/common` |
| RxJS | 7.x | Used for HTTP streams and complex async |
| NgRx | 17.x | Only for shared cross-feature state |
| Angular Material | 16+ | UI component library |
| TypeScript | 5.x | Strict mode enabled |

---

## Test Conventions

- Use `TestBed.configureTestingModule` with `imports: [ComponentUnderTest]` for standalone components
- Mock services with `jasmine.createSpyObj('ServiceName', ['methodA', 'methodB'])`
- Use `HttpClientTestingModule` and `HttpTestingController` for HTTP service tests
- Use `fakeAsync` + `tick()` for async operations; `flush()` for Promises
- Test `signal()` values by calling the signal as a function: `expect(component.customers()).toEqual([...])`
- Each spec file mirrors the source file: `customer-list.component.spec.ts`
- Test `OnPush` components by calling `fixture.detectChanges()` after state mutations
