# interview-scaler-repo
## WELCOME to common interview questions
## Rxjs questoion
https://gist.github.com/GeoffMahugu/bf4d16609b3a3eec02b9819f6ce92161

##  Angulr
https://codewithpawan.medium.com/angular-interview-questions-from-beginners-to-advance-part-2-c1f89892bee4
https://dev.to/railsstudent/create-type-ahead-search-using-rxjs-and-angular-standalone-components-4m4a
https://testbook.com/interview/angular-coding-interview-questions

These are **RxJS (Reactive Programming)** concepts. I’ll explain them **slowly, intuitively, and with examples**, assuming beginner level.

---

## 1️⃣ Hot vs Cold Observable

### ❄️ Cold Observable

* Starts **produucing data only when someone subscribes**
* Each subscriber gets **its own execution**
* Like **Netflix** → every user can start from beginning

**Example**

```ts
import { Observable } from 'rxjs';

const cold$ = new Observable(observer => {
  console.log('API called');
  observer.next(Math.random());
});

cold$.subscribe(v => console.log('Sub A:', v));
cold$.subscribe(v => console.log('Sub B:', v));
```

**Output**

```
API called
Sub A: 0.32
API called
Sub B: 0.78
```

✔ API called **twice**
✔ Each subscriber gets **different data**

---

### 🔥 Hot Observable

* Produces data **independent of subscribers**
* Subscribers **share the same data**
* Like **Live TV / Radio**

**Example**

```ts
import { fromEvent } from 'rxjs';

const hot$ = fromEvent(document, 'click');

hot$.subscribe(() => console.log('Sub A clicked'));
hot$.subscribe(() => console.log('Sub B clicked'));
```

✔ Click once → both subscribers receive it
✔ Data is **shared**

---

## 2️⃣ combineLatest

### What it does

* Combines **latest values from multiple observables**
* Emits **only after all observables emit at least once**
* Then emits whenever **any one emits**

### Syntax

```ts
combineLatest([obs1, obs2])
```

### Example

```ts
import { combineLatest, interval } from 'rxjs';

const obs1$ = interval(1000); // 0,1,2...
const obs2$ = interval(2000); // 0,1...

combineLatest([obs1$, obs2$])
  .subscribe(([a, b]) => console.log(a, b));
```

**Output**

```
1 0
2 0
3 1
4 1
```

✔ Waits for **both**
✔ Emits **continuously**

---

## 3️⃣ withLatestFrom

### What it does

* One observable is **main (trigger)**
* Others just provide **latest values**
* Emits **only when main emits**

### Example

```ts
import { interval } from 'rxjs';
import { withLatestFrom } from 'rxjs/operators';

const click$ = interval(1000);
const timer$ = interval(5000);

click$
  .pipe(withLatestFrom(timer$))
  .subscribe(([click, timer]) =>
    console.log('Click:', click, 'Timer:', timer)
  );
```

✔ Emits **only when click$ emits**
✔ timer$ does **not trigger emission**

---

### combineLatest vs withLatestFrom (Very Important)

| Feature      | combineLatest         | withLatestFrom |
| ------------ | --------------------- | -------------- |
| Who triggers | Any observable        | Only source    |
| Use case     | Sync multiple streams | Event + state  |

---

## 4️⃣ Subjects (Hot Observables)

A **Subject** is:

* Observable + Observer
* Multicasts values
* Always **HOT**

---

## 5️⃣ Subject

### Behavior

* No initial value
* New subscribers **miss old values**

```ts
import { Subject } from 'rxjs';

const sub = new Subject<number>();

sub.next(1);

sub.subscribe(v => console.log('A:', v));

sub.next(2);

sub.subscribe(v => console.log('B:', v));

sub.next(3);
```

**Output**

```
A: 2
A: 3
B: 3
```

✔ B missed `2`

---

## 6️⃣ BehaviorSubject

### Behavior

* Requires **initial value**
* New subscribers get **latest value immediately**

```ts
import { BehaviorSubject } from 'rxjs';

const beh = new BehaviorSubject<number>(0);

beh.subscribe(v => console.log('A:', v));

beh.next(1);

beh.subscribe(v => console.log('B:', v));
```

**Output**

```
A: 0
A: 1
B: 1
```

✔ Perfect for **state management**

---

## 7️⃣ ReplaySubject

### Behavior

* Replays **last N values** to new subscribers

```ts
import { ReplaySubject } from 'rxjs';

const replay = new ReplaySubject<number>(2);

replay.next(1);
replay.next(2);
replay.next(3);

replay.subscribe(v => console.log('Sub:', v));
```

**Output**

```
2
3
```

✔ Stores history
✔ Useful for caching

---

## 8️⃣ Subject vs Behavior vs Replay (Interview Favorite)

| Feature           | Subject | BehaviorSubject | ReplaySubject |
| ----------------- | ------- | --------------- | ------------- |
| Initial value     | ❌       | ✅               | ❌             |
| Replay old values | ❌       | Latest only     | Last N        |
| Hot               | ✅       | ✅               | ✅             |
| Common use        | Events  | State           | Cache         |

---

## 9️⃣ Real-World Mapping

| Scenario             | Use             |
| -------------------- | --------------- |
| Button clicks        | Subject         |
| Logged-in user state | BehaviorSubject |
| API cache            | ReplaySubject   |
| Form fields          | combineLatest   |
| Button + form state  | withLatestFrom  |

---

If you want next:

* 🔥 Marble diagram explanation
* 🔥 Angular real-life use cases
* 🔥 combineLatest vs forkJoin vs zip
* 🔥 Interview questions with traps

Just tell me 👍



