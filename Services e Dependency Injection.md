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