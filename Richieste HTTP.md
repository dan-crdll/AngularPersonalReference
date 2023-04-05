Se ad esempio volessimo accedere e salvare lo stato dell'applicazione su un database, non dovremmo mai conservare le credenziali di accesso al DB nell'app Angular in quanto si tratta di un framework front end e quindi chiunque potrebbe ispezionarne il codice, quindi bisogna comunicare con un server, attraverso le API usando il protocollo HTTP. Usare le API di un server è come visitare un sito web solo che invece di ricevere codice HTML si ricevono dati, spesso in formato JSON. Sarà poi il server, tramite il backend ad occuparsi della comunicazione con il database.
Una richiesta HTTP si compone dei campi:
```
HTTP Verb     URL(API Endpoint)      Headers(Metadata)       [Body]
```

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