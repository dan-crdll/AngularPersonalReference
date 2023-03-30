Attraverso le template variable si possono prendere reference all'elemento HTML in typescript.
```html
<div *ngIf="x < y then thenBlock else elseBlock"></div>

<ng-template #thenBlock> Then Block </ng-template>
<ng-template #elseBlock> Else Block </ng-template>
```
sono un metodo per puntare ai form.

Potremmo usare `ngModel` per collegare direttamente una variabile con un valore nel DOM ma non sempre è ciò che vogliamo fare, ad esempio:
```html
<input [(ngModel)]="value"/>
```
possiamo invece usare le template variable:
```html
<input #inputValue/>
```
Il riferimento locale potrà poi essere passato direttamente nelle chiamate ai metodi dovuti agli eventi.
Alle volte però abbiamo la necessità di accedere agli elementi riferiti attraverso una template variable prima che vengano chiamate delle funzioni, questo è possibile, nella classe TypeScript usando il decoratore `@ViewChild()`:
```ts
@Component(
	selector: 'app-test',
	templateUrl: 'test.component.html',
	styleUrls: ['test.component.css']
)
export class testComponent {
	@ViewChild('inputValue') valoreInput : ElementRef<HTMLInputElement>;
}
```
abbiamo quindi un figlio della View che si chiama `inputValue`.
Nella definizione della variabile bisogna essere quanto più specifici possibile nella tipizzazione per avere i suggerimenti dell'IDE.
In questo caso possiamo prendere il valore contenuto nel campo di testo di `input` con:
```ts
this.valoreInput.nativeElement.value
```
se volessimo usare `@ViewChild` all'interno del metodo `ngOnInit()` dovremmo impostare nei parametri `@ViewChild('name', {static: true})`.

Si potrebbero avere delle template variable anche nel contenuto che viene proiettato attraverso la direttiva `ng-content`, in questo caso non è possibile usare `@ViewChild()` perché effettivamente quel contenuto non fa parte del template, è però possibile usare una variabile con decoratore `@ContentChild()` che permette l'accesso al riferimento proiettato:
```ts
@ContentChild('projectedRef') ref : ElementRef<T>;
```
