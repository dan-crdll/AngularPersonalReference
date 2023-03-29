In Angular esistono quattro tipi di data binding : 
- String Interpolation
- Property Binding
- Event Binding
- Two Ways Binding

#### String Interpolation
La string interpolation consente di riportare il valore delle variabili all'interno del DOM in HTML, immaginiamo che nella classe TypeScript vi sia:
```ts
@Component({
	selector:'app-test',
	templateUrl:'./test.component.html',
	styleUrls:['./test.component.css']
})
export class TestComponent {
	title = 'Example Title';
}
```
immaginiamo di voler riportare il contenuto della variabile `title` all'interno di un heading html, allora useremo la string interpolation, nel file `test.component.html` scriveremo:
```html
<h5>{{ title }}</h5>
```
si possono usare anche operatori TypeScript e funzioni ma non si possono usare blocchi di codice.

#### Property Binding
Il property binding lo usiamo per legare delle proprietà del DOM al contenuto di variabili all'interno della classe TypeScript. Immaginiamo di avere il file `test.component.ts` come:
```ts
@Component({
	selector:'app-test',
	templateUrl:'./test.component.html',
	styleUrls:['./test.component.css']
})
export class TestComponent implements OnInit {
	isEnabled = true;
	
	ngOnInit() : void {
		setInterval( () => { isEnabled = !isEnabled }, 2000);
	}
}
```
e di comandare così lo stato di attivazione di un bottone nel file `test.component.html` attraverso il property binding:
```html
<button [disabled]="isEnabled"> Test Button </button>
```
ogni 2 secondi la variabile `isEnabled` cambierà valore e di conseguenza anche lo stato di attivazione del pulsante

#### Event Binding
L'event binding consente di associare degli eventi che avvengono sul DOM (ad esempio pressione di pulsanti) con funzioni nella classe TypeScript. Immaginiamo di avere il component:
```ts
@Component({
	selector:'app-test',
	templateUrl:'./test.component.html',
	styleUrls:['./test.component.css']
})
export class TestComponent {
	onClick(e : Event) : void {
		console.log(e);
	}
}
```
allora associamo la pressione del pulsante con la chiamata alla funzione `onClick()` passando come parametro l'evento:
```html
<button (click)="onClick($event)"> Test Button </button>
```

#### Two Ways Binding
Il binding a due vie è il tipo più complicato di binding, in questo caso si vuole un passaggio dal DOM al TypeScript e viceversa, si ascoltano degli eventi e simultaneamente si aggiornano dei valori. Immaginiamo di avere un form formato da uno spazio di input ed un pulsante che ripristina il valore, vogliamo che alla pressione del pulsante corrisponda una variazione sia del valore dell'input che di un valore stampato. Abbiamo il component:
```ts
@Component({
	selector:'app-test',
	templateUrl:'./test.component.html',
	styleUrls:['./test.component.css']
})
export class TestComponent {
	inputValue = "test input";

	onClick() : void {
		this.inputValue = "test input";
	}
}
```
allora usando la direttiva `ngModel` si può avere nel DOM:
```html
<input [(ngModule)]="inputValue"/>
<button (click)="onClick()"> Reset Button </button>
<h2>{{ inputValue }}<h2>
```
per poter usare la direttiva bisogna importare il component:
```ts
import {FormsModule} from '@angular/forms';
```
Con il binding a due vie quindi si riesce a rispondere agli eventi in entrambe le direzioni.