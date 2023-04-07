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

#### Modules
I moduli sono un modo per racchiudere diverse parti che compongono un'applicazione Angular, ogni applicazione deve avere almeno un modulo chiamato `AppModule`, Angular quindi analizza `@NgModule` per capire l'applicazione e le sue proprietà. Uno dei motivi che può portare alla suddivisione in moduli è quello di mantenere l'`app.module.ts` leggibile facilmente, senza includere troppo codice. La suddivisione in moduli permette inoltre di aumentare le performance dell'applicazione.
Un modo per dividere i moduli è quello di mantenere `AppModule` solo con `AppComponent` e spostare tutti gli altri component nei cosiddetti features module, cioè dei moduli che si occupano della gestione di alcune features dell'applicazione.
Il modulo `BrowserModule` deve essere importato solo una volta, se si vogliono usare alcune cose di questo modulo in altri moduli bisogna importare `CommonModule`.
I servizi anche se dichiarati solo nell'`AppModule` sono utilizzabili in tutta l'applicazione.
Possiamo inoltre anche scrivere su più file il routing relativo ad un modulo, utilizzando il metodo `RouterModule.forChild()` che conterrà i percorsi per il modulo stesso.
Inoltre per evitare la duplicazione del codice si possono usare dei moduli condivisi che vengono importati da tutti gli altri moduli che utilizzano gli stessi imports, è importante però ricordarsi che il modulo condiviso deve esportare i moduli che devono essere condivisi.
Creando un `CoreModule` è possibile liberare l'`AppModule` dai servizi per renderlo più pulito, i providers non devono essere esportati e basta importare il `CoreModule` in `AppModule`.

#### Lazy Loading
Con l'uso del lazy loading è possibile caricare solo i moduli presenti nella pagina con l'url che si sta caricando, senza caricare tutti gli altri moduli che potrebbero anche non venire mai utilizzati. Per poter implementare il Lazy Loading è necessario che il modulo dichiari i propri percorsi con `forChild()`. Successivamente bisogna andare nel modulo di routing principale e dichiarare di nuovo la base del percorso ma invece di scrivere un component si deve scriver:
```ts
const appRoutes: Routes = [
	{ path: '', redirectTo: '/recipes', pathMatch: 'full' },
	{ path: 'recipes', loadChilren: () => import('./recipes.module').then(m => m.RecipesModule) }
]
```
è inoltre necessario togliere tutti gli import inutilizzati dentro i moduli perché se no verranno caricati comunque nello stesso bundle.
Il Lazy Loading può essere ottimizzato precaricando il codice aggiungendo l'oggetto come secondo parametro della `forRoot()`:
```ts
RouterModule.forRoot(appRoutes, { preloadingStrategy: PreloadAllModules })
```
