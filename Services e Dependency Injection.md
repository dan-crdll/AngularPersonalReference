Il riutilizzo del codice e lo storing di dati sono i tipi utilizzi dei servizi, i servizi si possono vedere come delle repository centralizzate e delle unità di lavoro. 
Le istanze dei servizi non vanno create manualmente, ma angular fornisce un tool potente per poter usare i servizi dentro le nostre applicazioni, si tratta dell'Angular Dependency Injector. Il dependency injector instanzia automaticamente un'istanza della dipendenza all'interno del component in cui serve. Per informare Angular della necessità dell'injection bisogna usare il costruttore:
```ts
@Component({
	selector: 'app-test',
	templateUrl: 'test.component.hmtl',
	styleUrls: ['test.component.css']
})
export class TestComponent {
	constructor(private service: TestService) { }
}
```
questo non è però sufficiente, bisogna infatti specificare chi è il provider del servizio, per fare ciò includiamo nel decoratore la lista dei providers di servizi usati nel component:
```ts
@Component({
	selector: 'app-test',
	templateUrl: 'test.component.hmtl',
	styleUrls: ['test.component.css'],
	providers: [TestService]
})
export class TestComponent {
	constructor(private service: TestService) { }
}
```
quindi adesso si potrà accedere ovunque nel codice del component ai metodi e variabili pubbliche offerte dal servizio.
Un altro utilizzo che si fa dei servizi è quello di gestione e conservazione dei dati, come ad esempio quello di storing e gestione degli accounts.
L'injector di Angular è un injector gerarchico, ciò significa che quando iniettiamo un servizio in un component, la stessa istanza del servizio viene iniettata in tutti i figli e discendenti di quello stesso component nella gerarchia, la dichiarazione nei providers dei component figli però provoca un overwrite ed una conseguente nuova instanziazione del servizio, per questo motivo se si intende utilizzare la stessa istanza in uno o più components figli è necessario non inserire il provider ma inserire solo la variabile che conterrà il servizio dentro il constructor. 
Il livello più alto della gerarchia è `appComponent` quindi un'istanza inserita in questo component sarà utilizzabile nell'intera applicazione, a meno che non sia sovrascritta. 
Inoltre è possibile iniettare un servizio dentro un altro servizio ma per poterlo fare è necessario utilizzare il decorator `@Injectable()` nel servizio in cui si vuole iniettare, nei servizi dove non si vuole iniettare nulla non è necessario inserire questo decoratore ma è comunque buona pratica inserirlo:
```ts
@Injectable()
export class TestService {
	constructor(private addService: InjectedService) {}
}
```
Inoltre attraverso i servizi è possibile creare una comunicazione tra diversi components che hanno la stessa istanza del servizio attraverso l'emissione e la sottoscrizione ad eventi dichiarati nel servizio:
```ts
export class TestService {
	event1 = new EventEmitter<T>();
}
```
```ts
@Component({
	selector: 'app-test1',
	templateUrl: 'test1.component.html',
})
export class Test1Component {
	constructor(private testService: TestService) {}

	sendData(dataToSend: T) {
		this.testService.event1.emit(dataToSend);
	}
}
```
```ts
@Component({
	selector: 'app-test2',
	templateUrl: 'test2.component.html',
})
export class Test2Component implements OnInit {
	constructor(private testService: TestService) {}

	ngOnInit() : void {
		this.testService.event1.subscribe(() => { ... })
	}
}
```
Un altro modo per offrire servizi in modo globale all'applicazione nelle nuove versioni di Angular è quello di inserire al momento della dichiarazione del servizio, come parametro a `@Injectable()` l'oggetto:
```ts
@Injectable({providedIn: 'root'})
```
senza la necessità di inserirlo nei providers di appModule. Il vantaggio di questa soluzione è il caricamento 'lazy' dei servizi da parte di Angular e l'eliminazione automatica delle ridondanze, portando a vantaggi di performance sulle grandi applicazioni.