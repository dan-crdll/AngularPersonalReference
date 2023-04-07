Si può pensare ad un observable come una sorgente dati, viene importato da un pacchetto di terze parti `RxJS`. Per l'utilizzo di un observable serve un observer (il codice che legge i dati rilasciati dall'observable e li usa), l'observer può gestire dati, errori e gestire il completamento dell'observable. Gli observable sono solitamente usati per gestire i compiti asincroni. Sono una alternativa alle `Promise`.
Per poter usare gli observables si deve installare il pacchetto:
```sh
npm install --save rxjs@6
npm install --save rxjs-compat
```

Attraverso la funzione `interval(millisTime)` si può creare un observable che emette un numero ogni volta che passano `millisTime` millisecondi, il numero emesso è un counter.
Per richiamare una funzione ogni volta che un observable emette bisogna usare il metodo `subscribe()`.
Non è sempre vero che un observable smette di emettere quando non siamo più interessati a lui, per questo motivo è sempre necessario disiscriversi:
```ts
@Component({
	selector: 'app-observable-test',
	templateUrl: 'observable-test.component.html',
	styleUrls: ['observable-test.component.css']
})
export class ObservableTestComponent implements OnInit, OnDestroy {
	private firstObsSubscription: Subscription;

	ngOnInit() {
		this.firstObsSubscription = interval(1000).subscribe( count => {
			console.log(count);
		})
	}

	ngOnDestroy() {
		this.firstObsSubscription.unsubscribe();
	}
}
```
gli observable forniti da Angular vengono gestiti da Angular quindi non è necessario disiscriversi.

#### Custom Observable e ciclo di vita
Per creare un observable si usa il costruttore di `Observable` che prende come parametro una funzione alla quale automaticamente viene passato un parametro `observer`:
```ts
const customObservable = new Observable(observer => {...});
```
per emettere un nuovo valore dall'observer si usa il metodo `next()`:
```ts
@Component({
	selector: 'app-observable-test',
	templateUrl: 'observable-test.component.html',
	styleUrls: ['observable-test.component.css']
})
export class ObservableTestComponent implements OnInit {
	ngOnInit() {
		const customIntervalObs = new Observable<number>(observer => {
			let count = 0;
			setInterval(() => {
				observer.next(count++);
			}, 1000)
		});
		customIntervalObs.subscribe(data => {console.log(data)})
	}
}
```
Quando un observable emette un errore attraverso il metodo `error()` sull'`observer` smette di continuare, quindi non c'è bisogno di disiscriversi. Per gestire gli errori si passa una seconda funzione come parametro della subscribe:
```ts
@Component({
	selector: 'app-observable-test',
	templateUrl: 'observable-test.component.html',
	styleUrls: ['observable-test.component.css']
})
export class ObservableTestComponent implements OnInit {
	ngOnInit() {
		const customIntervalObs = new Observable<number>(observer => {
			let count = 0;
			setInterval(() => {
				observer.next(count++);
				if(count > 3) 
					observer.error(new Error('Count > 3!'))
			}, 1000)
		});
		customIntervalObs.subscribe(
			data => {console.log(data)},
			error => {console.log(error)});
	}
}
```
anche il completamento di un observable può essere gestito passando una terza funzione come parametro di subscribe:
```ts
@Component({
	selector: 'app-observable-test',
	templateUrl: 'observable-test.component.html',
	styleUrls: ['observable-test.component.css']
})
export class ObservableTestComponent implements OnInit {
	ngOnInit() {
		const customIntervalObs = new Observable<number>(observer => {
			let count = 0;
			setInterval(() => {
				observer.next(count++);
				if(count == 2)
					observer.complete();
				if(count > 3) 
					observer.error(new Error('Count > 3!'))
			}, 1000)
		});
		customIntervalObs.subscribe(
			data => {console.log(data)},
			error => {console.log(error)},
			() => {console.log('completed')});
	}
}
```

#### Operators
Una delle caratteristiche più importanti degli observables è l'utilizzo degli operators, un observable come detto emette dati e l'observer per utilizzarli deve iscriversi all'observable, gli operators si mettono di mezzo, quindi i dati che vengono emessi dall'observable arrivano all'operator e poi l'observer si iscrive all'operator che quindi permette di cambiare il modo in cui arrivano i dati.
```ts
@Component({
	selector: 'app-observable-test',
	templateUrl: 'observable-test.component.html',
	styleUrls: ['observable-test.component.css']
})
export class ObservableTestComponent implements OnInit {
	ngOnInit() {
		const customIntervalObs = new Observable<number>(observer => {
			let count = 0;
			setInterval(() => {
				observer.next(count++);
			}, 1000)
		});

		customIntervalObs.pipe(map( (data: number) => {
			return 'Round: ' + (data + 1);
		}))
		.subscribe(data => {console.log(data)});
	}
}
```
si possono usare più operatori come `map()`, `filter()` e molti altri.
Per concatenare più observable si usa l'operatore `exhaustMap()`.

#### Subjects
Un subject permette il passaggio di informazioni tra diversi component senza usare il metodo di emissione degli eventi, si tratta di un tipo particolare di observable.
Il `Subject` è qualcosa a cui ci si può iscrivere ma è più attivo di un `Observable` perché è possibile usare il metodo `next()` dall'esterno, è quindi perfetto per essere usato come emettitore di eventi.
Si tratta di un metodo più efficiente degli `EventEmitter` però bisogna ricordarsi sempre di disiscriversi dal `Subject` quando non è più necessario per non avere memory leaks. 
Non si usano i `Subject` quando si hanno parametri `@Output()`, i `Subject` si usano solo per la comunicazione tra component.
Esistono inoltre i `BehaviourSubject` che permettono l'accesso anche ai valori emessi precedentemente anche se ancora non ci si era iscritti, in questo caso è utile l'operatore `take(1)` che restituisce l'ultimo valore emesso e si disiscrive automaticamente.