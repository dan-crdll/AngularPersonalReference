Se ad esempio volessimo accedere e salvare lo stato dell'applicazione su un database, non dovremmo mai conservare le credenziali di accesso al DB nell'app Angular in quanto si tratta di un framework front end e quindi chiunque potrebbe ispezionarne il codice, quindi bisogna comunicare con un server, attraverso le API usando il protocollo HTTP. Usare le API di un server è come visitare un sito web solo che invece di ricevere codice HTML si ricevono dati, spesso in formato JSON. Sarà poi il server, tramite il backend ad occuparsi della comunicazione con il database.
Una richiesta HTTP si compone dei campi:
- HTTP Verb
- URL (API Endpoint)
- Headers (Metadata)
- Body

#### Richieste POST
Per potere effettuare richieste HTTP bisogna importare in `app.module.ts` il modulo `HttpClientModule`, dopo dovremo iniettare un'istanza di `HttpClient` nei component dai quali vogliamo inviare richieste. 
```ts
@Component({
	selector: 'app-post-test',
	templateUrl: 'post-test.component.html',
	styleUrls: ['post-test.component.css']
})
export class PostTestComponent {
	constructor(private http: HttpClient) {}

	onCreatePost(postData: {title: string, content: string}) {
		this.http.post('URL', postData);
	}
}
```
gli oggetti vengono automaticamente trasformati in JSON. Questa richiesta scritta così però non viene inviata!
Il metodo restituisce un observable, se non ci si iscrive la richiesta non viene inviata.
```ts
@Component({
	selector: 'app-post-test',
	templateUrl: 'post-test.component.html',
	styleUrls: ['post-test.component.css']
})
export class PostTestComponent {
	constructor(private http: HttpClient) {}

	onCreatePost(postData: {title: string, content: string}) {
		this.http.post('URL', postData)
				 .subscribe( data => { console.log(data); });
	}
}
```
in questo modo la richiesta verrà inviata al server. 

#### Richieste GET
Per mandare una richiesta GET si procede in modo quasi analogo per quanto visto con le POST:
```ts
@Component({
	selector: 'app-post-test',
	templateUrl: 'post-test.component.html',
	styleUrls: ['post-test.component.css']
})
export class PostTestComponent {
	constructor(private http: HttpClient) {}

	fetchData() {
		this.http.get('URL')
				 .subscribe( data => { console.log(data); });
	}
}
```
quello che ci viene restituito è un oggetto JavaScript che deve essere trasformato, per la trasformazione useremo gli operators degli observable.
```ts
@Component({
	selector: 'app-post-test',
	templateUrl: 'post-test.component.html',
	styleUrls: ['post-test.component.css']
})
export class PostTestComponent {
	constructor(private http: HttpClient) {}

	fetchData() {
		this.http.get<{[key: string]: PostObject}>('URL')
				 .pipe( map((response) => {
					 const responseArray = [];
					 for(let key in response)
						 if(response.hasOwnProperty(key))
							 responseArray.push({...response[key],id:key})
					 return responseArray;
				 }))
				 .subscribe( data => { console.log(data); });
	}
}
```
in questo modo avremo tutti gli oggetti ricevuti all'interno di un array che ne permette una gestione più agevole. Il metodo `get` è inoltre un generico all'interno del quale si può definire il tipo della risposta che viene restituita.

#### Usare servizi per le richieste HTTP
Nelle applicazioni più grandi si potrebbero avere component con molto codice non direttamente correlato con l'interfaccia utente. I servizi sono la parte dell'applicazione che si occupa del lavoro di calcolo o trasformazione dei dati, mentre i component dovrebbero avere quasi solo codice correlato con il template. 
Per questo motivo è una buona pratica spostare la parte correlata alla richiesta HTTP in un servizio mentre la parte correlata al template deve rimanere all'interno del component.

#### Gestione degli errori
Quando si comunica con il backend possono capitare diversi errori, bisogna gestirli. Quando ci si iscrive ad un observable si può passare una funzione per il caso in cui la sottoscrizione vada a buon fine, però si può passare anche una funzione che viene eseguita solo in caso di errore. 
```ts
this.postsService.fetchPosts().subscribe({
	next: (data) => {
		this.doSomethingWithData(data);
	},
	error: (error: Error) => {
		console.log(error.message);
	}
})
```
nel caso in cui la sottoscrizione ad un Observable avvenga direttamente dentro un servizio, ma l'errore interessa ad altre parti dell'applicazione, si può creare un `Subject`.
Inoltre è anche possibile usare l'operator `catchError()` dopo il `map()` operator nella pipe, per trasformare e gestire l'errore in base a quello che si vuole fare, dentro al quale si ritorna un altro observable che incorpori l'errore attraverso il metodo `throwError()`.

#### Parametri della richiesta
###### Headers
Quando si invia una richiesta HTTP si specifica il metodo HTTP usato, e se necessario anche i dati che devono essere inviati, alle volte è necessario però inviare anche degli headers HTTP per diversi motivi come l'autenticazione.
Ogni metodo di richiesta HTTP ha un argomento extra che è un oggetto dove si può configurare la richiesta, il primo dei campi è il campo `headers`:
```ts
this.http.get<ContentType>(url, 
						   {headers: new HttpHeaders({
							   'CustomHttpHeader': 'hello',
						   })})
```
dipende poi dall'API che si sta utilizzando.

###### Query Params
Inoltre è possibile inviare dei parametri di query:
```ts
this.http.get<ContentType>(url, 
						   {headers: new HttpHeaders({
							   'CustomHttpHeader': 'hello',
						   }),
						   params: new HttpParams()
							   .set('print', 'pretty'),
							})
```
per usare più di un parametro bisogna dichiarare una variabile:
```ts
let searchParams = new HttpParams();
searchParams = searchParams.append('print', 'pretty');
```
si potrebbero anche includere nell'url ma usando questo metodo si ha una soluzione più pulita evitando stringhe troppo lunghe.

##### Tipi di risposta
Quello che di default ci viene mostrato quando otteniamo la risposta HTTP è il suo corpo, in realtà impostando nell'oggetto un campo `observe` è possibile ottenere l'intera risposta:
```ts
this.http.post(url, {
			observe: 'response'
		}
	);
```
usando `observe: 'events'` si ottengono informazioni su quello che accade dal lato backend.
Inoltre è possibile anche cambiare il tipo del body della risposta che si riceve, di default è impostato su json ma è possibile ottenere anche testi o blob, impostando il campo `responseType: 'json'`.

#### Interceptors
Immaginiamo di voler applicare a tutte le richieste HTTP che inviamo un certo header, nell'approccio visto precedentemente dovremmo includerlo in ogni richiesta. Per evitare di inserirlo manualmente in ogni richiesta possiamo creare un interceptor. Creiamo dunque un nuovo file `name-interceptor.service.ts` quindi:
```ts
export class NameInterceptorService implements HttpInterceptor {
	intercept(req: HttpRequest<KindOfData>, next: HttpHandler) {
		console.log('Request is on its way');
		return next.handle(req);
	}
}
```
Il codice all'interno del metodo `intercept()` viene eseguito prima che la richiesta HTTP lasci la nostra applicazione, dopo viene passata la richiesta al metodo `handle` di `next` che permette alla richiesta di continuare il suo percorso.
Per utilizzare l'interceptor bisogna fornirlo, per farlo bisogna includerlo nei `providers` di `app.module.ts` come oggetto JavaScript:
```ts
...
providers: [{
		provide: HTTP_INTERCEPTORS, 
		useClass: NameInterceptorService,
		multi: true
	}],
...
```
All'interno di `intercept` si può manipolare la richiesta, la `req` passata però è immutabile quindi bisogna crearne una nuova clonandola:
```ts
const modifiedRequest = req.clone({url: 'new url', ...})
```
e poi si invia la richiesta modificata.

Non siamo limitati ad usare gli interceptor con le richieste, ma possiamo usarli anche con le risposte. Il metodo `handle()` restituisce infatti un observable sul quale possiamo usare `pipe()` e passare degli operatori tra cui `tap()` che ci permette di controllare la risposta senza restituire nulla, e quindi farla continuare nella pipe. Nell'interceptor però si hanno sempre `events` così che si possa avere un controllo più accurato sulla risposta stessa. 

#### Authentication e Route Protection
La maggior parte delle applicazioni richiede un processo di autenticazione per essere usate o per accedere ad alcune parti. 
Quando un utente inserisce le informazioni di autenticazione nel client, queste vengono inviate al server dove vengono validate, in una applicazione tradizionale il server risponderebbe con una sessione, ma nel caso delle applicazioni Angular si hanno delle single page application quindi il routing viene gestito direttamente da Angular, il server è quindi una RESTful API, quindi stateless, quindi il server è una API e di conseguenza non si occupa del rendering delle pagine, il front end ed il back end sono completamente sganciati. Si usa quindi un altro approccio, il server valuta le credenziali inserite e se sono valide risponde con un token, solitamente un json web token. Il token viene generato con un algoritmo che conosce solo il server, quindi l'idea è che il client conservi il token da qualche parte e poi lo invii al server per autorizzare le richieste successive. 
Dopo aver inviato quindi la richiesta HTTP per il sign in al server riceveremo un token che eventualmente potrebbe anche scadere. Creiamo quindi un modello per il nostro utente:
```ts
export class User {
	constructor(
		public email: string,
		public id: string,
		private token: string,
		private tokenExpirationDate: Date
	) {}

	get token() {
		if(this.tokenExpirationDate || 
			new Date() > this.tokenExpirationDate) {
			return this.token;
		}
		return null;
	}
}
```
nel servizio di autenticazione quindi possiamo salvare l'utente come `Subject` in modo da poter inviare degli eventi. 
Dopo aver fatto il login possiamo pensare di avere un reindirizzamento ad una pagina a cui si è autorizzati. Questo ovviamente viene fatto con il router all'interno della parte success della sottoscrizione all'observable della richiesta HTTP.
Nella comunicazione con il backend dobbiamo poi passare il token per effettuare le richieste che altrimenti non andranno a buon fine. 
Possiamo salvare il token nel local storage del browser:
```ts
localStorage.setItem('userData', JSON.stringify(user));
```
per recuperarlo quando l'applicazione si restarta invece si scrive:
```ts
autoLogin() {
	const userData = localStorage.getItem('userData');
	if(!userData) 
		return;
	const loadedUser = new User(userData.email, userData._token);

	if(loadedUser.token) {
		this.user.next(loadedUser);
	}
}
```
