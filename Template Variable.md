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
nella classe TypeScript dobbiamo poi usare il decoratore `@ViewChild()`:
```ts
@Component(
	selector: 'app-test',
	templateUrl: 'test.component.html',
	styleUrls: ['test.component.css']
)
export class testComponent {
	@ViewChild('inputValue') valoreInput !: ElementRef<HTMLInputElement>;
}
```
abbiamo quindi un figlio della View che si chiama `inputValue`.
Nella definizione della variabile bisogna essere quanto più specifici possibile nella tipizzazione per avere i suggerimenti dell'IDE.
In questo caso possiamo prendere il valore contenuto nel campo di testo di `input` con:
```ts
this.valoreInput.nativeElement.value
```
