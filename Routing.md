Attraverso il routing di Angular è possibile creare una single page application che simula il passaggio tra pagine diverse attraverso la modifica dell'URL rimanendo comunque all'interno in realtà della stessa pagina.
Il router deve essere disponibile nell'intera applicazione, infatti da dovunque si digiti un URL specifico per un component deve essere possibile raggiungerlo, per questo motivo dobbiamo modificare `app.module.ts` aggiungendo una nuova costante:
```ts
const appRoutes: Routes = [
	{ path: 'users', component: UsersComponent },
	...
];
```
la costante è un array di `Route`. Possiamo anche impostare un percorso vuoto che porti alla home:
```ts
const appRoutes: Routes = [
	{ path: '', component: HomeComponent },
	{ path: 'users', component: UsersComponent },
	...
]
```
dobbiamo registrare queste rotte nella nostra app, nel vettore `Imports` dobbiamo aggiungere il `RouterModule.forRoot(appRoutes)`, in questo modo mettiamo Angular a conoscenza delle nostre routes. Per poter informare su dove renderizzare i components in base alla route dobbiamo usare la direttiva:
```html
<router-outlet><\router-outlet>
```
questa direttiva permette di impostare il punto del documento dove vogliamo che il router carichi il component corrispondente alla route selezionata.
Si potrebbe pensare di implementare la navigazione usando l'attributo `href` del tag `<a>`, in realtà questo non è un buon modo di farlo perché provoca il ricaricamento della pagina e quindi dell'applicazione, perdendo tutti gli stati della sessione. Per implementare in modo corretto la navigazione tra i diversi components si usa la direttiva `routerLink` passando la stringa corrispondente al percorso.
Utilizzando un link del tipo `\path` si implica l'utilizzo di un percorso assoluto quindi questa parte del percorso verrà aggiunta alla radice, se invece si usasse `path` si starebbe implicando un percorso relativo, quindi questa parte verrebbe aggiunta al percorso su cui già si è.

Attraverso la direttiva `routerLinkActive` si può passare come parametro una classe da assegnare all'elemento su cui la direttiva viene inserita quando il link associato all'elemento o ad uno dei suoi figli è attivo, bisogna però fare attenzione che basta che solo una parte del link sia presente affinché la classe venga mantenuta, per modificare questo comportamento di default si deve usare la direttiva `routerLinkActiveOptions`, ad esempio:
```html
<li 
	routerLinkActive="active"
	[routerLinkActiveOptions]="{exact: true}">
	...
<\li>
```
in questo caso quindi la classe viene assegnata solo se l'URL è esatto.

#### Routing via codice
Si può anche effettuare il routing verso un component direttamente attraverso il codice in TypeScript, per fare ciò dobbiamo iniettare nella classe un'istanza del router:
```ts
 @Component({
	 selector: 'app-test',
	 templateUrl: 'test.component.html',
	 styleUrls: ['test.component.css'],
 })
 export class TestComponent {
	constructor(private router: Router) {}

	onLoadPage() {
		this.router.navigate(['/page']);
	}
 }
```
quando si utilizza il metodo `navigate`, il `Router` non conosce il luogo in cui ci troviamo, per poter usare quindi i percorsi relativi bisogna iniettare il percorso attualmente attivo e passare un secondo argomento al metodo:
```ts
 @Component({
	 selector: 'app-test',
	 templateUrl: 'test.component.html',
	 styleUrls: ['test.component.css'],
 })
 export class TestComponent {
	constructor(private router: Router, private route: ActivatedRoute) {}

	onLoadPage() {
		this.router.navigate(['page'], {relativeTo: this.route});
	}
 }
```

#### Passaggio di parametri al percorso
Si possono creare dei percorsi dinamici per permettere l'utilizzo del routing anche con dei parametri senza la necessità di impostare un percorso per ogni component ad esempio facente parte una lista.
```ts
const appRoutes: Routes = [
	{ path: '', component: HomeComponent },
	{ path: 'users', component: UsersComponent },
	{ path: 'users/:id', component: UserComponent },
	...
]
```
potremo successivamente anche recuperare il parametro dall'URL, per fare ciò dobbiamo iniettare ovviamente il riferimento al link attualmente attivo:
```ts
@Component({
	 selector: 'app-test',
	 templateUrl: 'test.component.html',
	 styleUrls: ['test.component.css'],
 })
 export class TestComponent implements OnInit {
	user: {id: number, name: string};
	constructor(private route: ActivatedRoute) {}

	ngOnInit(): void {
		this.user = {
			id: this.route.snapshot.params['id'],
			name: somewhere['id']
		};
	}
 }
```
Questo approccio però non è reattivo, infatti una volta che un component viene instanziato i dati non vengono più modificati se viene ricaricato dall'interno del component stesso in quanto Angular instanzia il component una sola volta. Si deve quindi utilizzare un approccio con gli observables:
```ts
this.route.params.subscribe((params) => {
		this.user.id = params['id'];
	});
```
gli observables permettono la gestione dei task asincroni. 

###### Passaggio di parametri di querying e fragments
Per parametri di querying si intendono quei parametri che sono preceduti da un punto interrogativo e separati da e commerciale:
```
https:\\localhost:4200\users?editMode=true&par=0
```
per fragments invece si intendono quei parametri preceduti da un hash e che permettono di raggiungere posizioni specifiche all'interno della pagina, ad esempio 
```
https:\\localhost:4200\users#top
```
Per poter passare dei parametri di querying si deve usare la direttiva:
```html
<a [routerLink]="['\servers', 4, 'edit']"
   [queryParams]="{param1: 9}"><\a>
```
clickando il link si arriverà all'URL:
```
https:\\localhost:4200\servers\4\edit?param1=9
```
allo stesso modo usando la direttiva `fragment` è possibile passare il parametro fragment.
Se si vuole effettuare il routing via codice, è possibile passare, nell'oggetto del secondo parametro del metodo `navigate()` , i parametri di query e di fragment:
```ts
this.router.navigate(['\servers', id, 'edit'], {
					queryParams: {param1: 9}, 
					fragment:'load'});
```
Attraverso il riferimento al percorso attivo è possibile ottenere anche i parametri di querying e quelli di fragment, come prima è possibile farlo in due modi:
```ts
this.route.snapshot.queryParams;
this.route.snapshot.fragment;

this.route.queryParams.subscribe(...);
```

#### Nested Routing
