Le direttive permettono di aggiungere dei comportamenti alle applicazioni Angular, si dividono in tre tipi:
- Components
- Attribute Directives
- Structural Directives

I components  sono infatti delle directives con un template, esistono poi le directives senza template. 

#### Attribute Directives
Le attribute directives cambiano l'aspetto o il comportamento di un elemento, component o di un'altra direttiva, ascoltano e modificano il comportamento degli elementi HTML, attributi, proprietà e componenti. 
Si possono aggiungere o rimuovere classi CSS simultaneamente attraverso la direttiva `ngClass`, un esempio con l'operatore ternario è:
```html
<div [ngClass]="getClass()"> ... </div>
```
Usando la direttiva `ngStyle` si possono impostare stili diversi simultaneamente in base allo stato del component:
```html
<div ngStyle="{'background-color':'yellow'}"> ... </div>
```

#### Structural Directives
Le direttive strutturali sono responsabili del layout HTML modificandolo, aggiungendo, togliendo e manipolando gli elementi a cui sono attaccati.
Si può aggiungere o rimuovere un elemento aggiungendo una direttiva `ngIf` all'elemento, se la condizione è falsa Angular rimuove un elemento ed i suoi discendenti dal DOM.
```html
<div *ngIf="isActive"> ... </div>
```
Si può anche creare un blocco else utilizzando un riferimento locale al blocco:
```html
<div *ngIf="isActive"; else #notActiveBlock> Active! </div>
<ng-template #noActiveBlock> Not Active! </ng-template>
```
Con la direttiva `ngFor` invece si presenta una lista di oggetti.
```html
<div *ngFor="let item of items"> {{ item.name }} </div>
```
si può anche ottenere l'indice aggiungendo separato da un semicolon la stringa `let i=index`.
Per evitare la ripetizione di `ngIf` si può usare la direttiva `ngSwitch`:
```html
<div [ngSwitch]="monster.type">
	<div *ngSwitchCase="'zombie'"> ... </div>
	<div *ngSwitchCase="'skeleton'"> ... </div>
	<div *ngSwitchDefault> ... </div>
</div>
```

#### Creazione di direttive custom
Per creare una direttiva custom si usa il comando di CLI:
```bash
ng generate directive test
```
il file `directiveNam.directive.ts` che viene creato conterrà:
```ts
import { Directive } from '@angular/core';

@Directive({
  selector: '[test]'
})
export class TestDirective {

}
```
il secondo passo è quello di importare la classe `ElementRef` per avere accesso agli elementi del DOM attraverso la proprietà `nativeElement`, per iniettare la reference all'elemento del DOM utilizzo il costruttore:
```ts
@Directive({
	selector: '[test]'
})
export class TestDirective {
	constructor(private el : ElementRef) {
		...
	}
}
```
Si può essere più specifici usando i generics. Per applicare la direttiva ad un elemento nel DOM si scrive:
```html
<p test> Testing </p>
```
Si può anche fare in modo che la direttiva risponda a particolari eventi che avvengono a causa dell'input utente, ad esempio l'entrata del mouse o la sua uscita attraverso il decoratore `@HostListener`:
```ts
@Directive({
	selector:'[appTest]'
})
export class TestDirective {
	constructor(private el : ElementRef<HTMLElement>){}
	
	@HostListener('mouseenter') onMouseEnter() {
		this.colour('red');
	}

	@HostListener('mouseleave') onMouseLeave() {
		this.colour('');
	}

	colour(col : string) : void {
		this.el.nativeElement.style.color = col;
	}
}
```

Si possono passare i parametri alla direttiva aggiungendo una variabile con decorator `@Input()`, ad esempio:
```ts
@Directive({
	selector:'[appTest]'
})
export class TestDirective {
	constructor(private el : ElementRef<HTMLElement>) {}
	
	@Input() appTest !: string;
	@HostListener('mouseenter') onMouseEnter() {
		this.el.nativeElement.style.color = this.appTest;
	}
	@HostListener('mouseleave') onMouseLeave() {
		this.el.nativeElement.style.color = '';
	}
}
```
