I dynamic components sono dei components che vengono caricati in modo dinamico dal codice.
Per caricare un component dal programma si può usare l'approccio con `*ngIf*` oppure con il dynamic component loader, che permette di non toccare il template. Per poter creare un component dal codice bisogna usare la component factory
```ts
@Component({
	selector: 'app-test',
	templateUrl: 'test.component.html',
	styleUrls: ['test.component.css']
})
export class TestComponent {
	constructor(private cmpntFctryRes: ComponentFactoryResolver) {}

	private showComponent() {
		const cmpFactory = this.cmpntFctryRes.resolveComponentFactory(OthCmpnt);
		
	}
}
```
bisogna creare una direttiva per specificare dove deve essere messo il component:
```ts
@Directive({
	selector: '[appPlaceholder]'
})
export class PlaceholderDirective {
	constructor(public viewContainerRef: ViewContainerRef) {}
}
```
nel component di partenza quindi creiamo nel template:
```html
<ng-template appPlaceholder></ng-template>
```
quindi possiamo continuare:
```ts
@Component({
	selector: 'app-test',
	templateUrl: 'test.component.html',
	styleUrls: ['test.component.css']
})
export class TestComponent {
	@ViewChild(PlaceholderDirective) cmptHost: PlaceholderDirective;
	constructor(private cmpntFctryRes: ComponentFactoryResolver) {}

	private showComponent() {
		const cmpFactory = this.cmpntFctryRes.resolveComponentFactory(OthCmpnt);
		const hostViewContainerRef = this.cmptHost.viewContainerRef;
		hostViewContainerRef.clear();
		hostViewContainerRef.createComponent(cmpFactory);
	}
}
```
per versionio di Angular minori della 9.x questo crea un errore perché dobbiamo aggiungere il component che vogliamo creare dinamicamente all'oggetto passato a `@NgModule()` in `app.module.ts` come:
```ts
@NgModule({
	...
	entryComponents: [
		OthCmpnt,
	]
})
```
per poter passare i parametri per data binding bisogna salvare il component e usare la proprietà `instance`:
```ts
@Component({
	selector: 'app-test',
	templateUrl: 'test.component.html',
	styleUrls: ['test.component.css']
})
export class TestComponent {
	@ViewChild(PlaceholderDirective) cmptHost: PlaceholderDirective;
	constructor(private cmpntFctryRes: ComponentFactoryResolver) {}

	private showComponent() {
		const cmpFactory = this.cmpntFctryRes.resolveComponentFactory(OthCmpnt);
		const hostViewContainerRef = this.cmptHost.viewContainerRef;
		hostViewContainerRef.clear();
		const cmp = hostViewContainerRef.createComponent(cmpFactory);

		cmp.instance.property1 = 'property';
		sub = cmp.instance.property2.subscribe();
	}
}
```