##### View Incapsulation
Immaginiamo di avere due component, `app-parent` e `app-child`, se applichiamo uno stile al genitore:
```css
p {
	color: blue;
}
```
questo varrà solo per i paragrafi all'interno del template del genitore e non per quelli all'interno del figlio. Questo ovviamente non è il comportamento normale di CSS che altrimenti si applicherebbe a cascata all'interno della gerarchia, ma è un comportamento forzato da Angular attraverso l'aggiunta di identificatori specifici per i component, il comportamento prende il nome di View Incapsulation.
Questo comportamento può essere cambiato nella classe TypeScript del component:
```ts
@Component({
	selector: 'app-test',
	templateUrl: '.\test.component.html',
	styleUrls: ['.\test.component.css'],
	encapsulation: ViewEncapsulation.<None | Emulated | ShadowDom>
})
```
il comportamento di default è `Emulated`, se si impostasse `None` si avrebbe che gli stili impostati in `test.component.css` avrebbero rilevanza globale, invece `ShadowDom` si comporta come `Emulated` solo nei browser che supportano lo `ShadowDom`.