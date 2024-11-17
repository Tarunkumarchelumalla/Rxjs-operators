# RxJS Mapping Operators: `switchMap`, `mergeMap`, `concatMap`, and `concat`

## 1. `switchMap`

- **Description**: Switches to a new observable each time a new value is emitted from the source observable, canceling any previous inner observable.
- **Use Case**: Useful when you need to handle only the latest request (e.g., for typeahead or search).
- **Behavior**: Cancels the previous subscription and starts the new one.

**Example**:
```typescript
import { of, interval } from 'rxjs';
import { switchMap, take } from 'rxjs/operators';

interval(1000).pipe(
  switchMap(() => of('A', 'B', 'C').pipe(take(3)))
).subscribe(value => console.log(value));

// Output (restarts each second, so you only see the latest set):
// A, B, C (repeats)
```

## 2. `mergeMap`

- **Description**: Maps each value to an observable and merges all inner observables, allowing them to run concurrently.
- **Use Case**: When you need to perform parallel operations without waiting for previous ones to complete.
- **Behavior**: Runs all inner observables in parallel.

**Example**:
```typescript
import { of } from 'rxjs';
import { mergeMap, delay } from 'rxjs/operators';

of(1, 2, 3).pipe(
  mergeMap(value => of(`Result ${value}`).pipe(delay(1000)))
).subscribe(result => console.log(result));

// Output (values may overlap as they run in parallel):
// Result 1
// Result 2
// Result 3 (all after ~1 second)
```

## 3. `concatMap`

- **Description**: Maps each value to an observable and subscribes to them one by one in sequence.
- **Use Case**: When the order of operations matters and each must complete before the next starts.
- **Behavior**: Runs one inner observable at a time in order.

**Example**:
```typescript
import { of } from 'rxjs';
import { concatMap, delay } from 'rxjs/operators';

of(1, 2, 3).pipe(
  concatMap(value => of(`Result ${value}`).pipe(delay(1000)))
).subscribe(result => console.log(result));

// Output (sequential, one per second):
// Result 1
// Result 2
// Result 3
```

## 4. `concat`

- **Description**: Combines multiple observables and subscribes to them sequentially, emitting values from one observable after the previous one completes.
- **Use Case**: When you need to concatenate observables in a specific order.
- **Behavior**: Starts each observable only after the previous completes.

**Example**:
```typescript
import { of, concat } from 'rxjs';
import { delay } from 'rxjs/operators';

const obs1 = of('First').pipe(delay(1000));
const obs2 = of('Second');
const obs3 = of('Third');

concat(obs1, obs2, obs3).subscribe(result => console.log(result));

// Output:
// First (after 1 second)
// Second
// Third
```

## Summary Table

| Operator     | Description                                  | Concurrency | Order      |
|--------------|----------------------------------------------|-------------|------------|
| `switchMap`  | Switches to new observable, cancels previous | High        | No         |
| `mergeMap`   | Merges all inner observables concurrently    | High        | No         |
| `concatMap`  | Maps and subscribes one by one               | Low         | Yes        |
| `concat`     | Combines observables sequentially            | N/A         | Yes        |

These operators are essential for handling different scenarios in reactive programming, allowing for control over the concurrency and flow of data streams.
