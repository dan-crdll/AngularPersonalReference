#### Passaggio da parent a child
Immaginiamo di avere due components: `childComponent` e `parentComponent` e di avere quindi una situazione del tipo:
```html
<!-- File parent.component.html -->
<app-child></app-child>
```
supponiamo poi che all'interno della classe `parentComponent` ci sia un dato che debba essere passato dal padre al figlio:
```ts
@Component({
	selector: 'app-parent',
	templateUrl: 'parent.component.html',
	styleUrls: ['parent.component.css']
})
export class parentComponent {
	data = [
		{
			name: 'Data1',
			type: 'product'
		},
		{
			name: 'Data2',
			type: 'service'
		},
		{
			name: 'Data3',
			type: 'product'
		},
	];
}
```
quindi vogliamo poter passare questi dati alla classe figlio, per farlo bisogna utilizzare il decoratore `@Input()`:
```ts
@Component({
	selector: 'app-child',
	templateUrl: 'child.component.html',
	styleUrls: ['child.component.css']
})
export class childComponent {
	@Input() dataFromParent : any;
}
```
per poter passare questo dato dal parent al child bisogna utilizzare il property binding, quindi nel file `parent.component.html` scriveremo:
```html
<app-child [dataFromParent]="data"></app-child>
```

#### Passaggio da child a parent
Per effettuare il passaggio di dati dal component child al component parent bisogna utilizzare il sistema di emissione degli eventi e il decorator `@Output`, quindi la classe `childComponent` sarà:
```ts
@Component({
	selector: 'app-child',
	templateUrl: 'child.component.html',
	styleUrls: ['child.component.css']
})
export class childComponent {
	clickCount = 10;
	@Output() dataToExport = new eventEmitter<number>();

	sendData() {
		this.dataToExport.emit(this.clickCount);
	}
}
```
ad esempio potremmo collegare questo invio dei dati alla pressione di un bottone, quindi nel template html del child useremo l'event binding:
```html
<button (click)="sendData()"> Send Data </button>
```
dal lato del parent dobbiamo utilizzare un listener per la ricezione dei dati:
```ts
@Component({
	selector: 'app-parent',
	templateUrl: 'parent.component.html',
	styleUrls: ['parent.component.css']
})
export class parentComponent {
	data !: number;

	receiveData(e : any) : void {
		this.data = e;
	}
}
```
quindi con l'event binding assegniamo il listener attraverso l'html:
```html
<app-child (dataToExport)="receiveData($event)"></app-child>
```
