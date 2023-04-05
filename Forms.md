Attraverso la gestione dei forms da parte di Angular è possibile ovviamente ottenere i dati che sono stati inserire, ma è anche possibile controllare la validità del form. Angular da una rappresentazione del form come oggetto JavaScript avendo la possibilità quindi di gestirlo nel codice.
Angular permette due approcci per l'utilizzo dei forms: 
- Approccio template-driven: si imposta il form nel template ed Angular automaticamente deduce il form per iniziare in modo veloce
- Approccio reattivo: il form viene creato attraverso il codice TypeScript ed il codice HTML e poi i due vengono connessi manualmente, per avere un controllo maggiore sul form stesso
Bisogna importare nell'`app.module.ts` il `FormsModule` attraverso il quale Angular creerà automaticamente una rappresentazione in JavaScript ogni volta che incontra il tag `<form>`. Angular però non rileverà automaticamente l'input del form, non tutto del form infatti fa parte di quello che poi vorremo inviare al server. 

#### Template-Driven approach
In questo caso scegliere quello che vogliamo includere nell'oggetto JavaScript dal form è molto semplice, negli `<input/>` bisogna utilizzare la direttiva `ngModel` per indicare che quell'input è un control del form, bisogna passare come parametro il nome di quell'informazione attraverso l'attributo html `name`.
```html
<input type="text" id="user-name" ngModel name="username"/>
```
per rendere il form inviabile non possiamo aggiungere un click listener al pulsante di submit perché il comportamento di default è un altro, per questo motivo Angular mette a disposizione una direttiva che permette appunto di scegliere il comportamento da seguire quando si clicka il submit, in questo caso quindi bisogna modificare il tag `<form>`:
```html
<form (ngSubmit)="onSubmit()">
	...
</form>
```
per accedere ai contenuti del form che è stato inviato potremmo usare le template variable, però in un modo particolare:
```html
<form (ngSubmit)="onSubmit(f)" #f="ngForm">
	...
</form>
```
per accedere al form quindi si usa TypeScript:
```ts
@Component({
	selector: 'app-form-test',
	templateUrl: 'from-test.component.html',
	styleUrls: ['from-test.component.css']
})
export class FormTestComponent {
	onSubmit(form: NgForm) {
		console.log(form);
	}
}
```
si può anche accedere in una maniera alternativa:
```ts
@Component({
	selector: 'app-form-test',
	templateUrl: 'from-test.component.html',
	styleUrls: ['from-test.component.css']
})
export class FormTestComponent {
	@ViewChild('f') signupForm: NgForm;

	onSubmit() {
		console.log(this.signupForm);
	}
}
```
questo secondo metodo è più utile se si vuole accedere ai campi non solo quando si invia il form ma anche prima. Avendo il riferimento al form possiamo modificare tramite codice i singoli campi attraverso il metodo `patchValue()`:
```ts
@Component({
	selector: 'app-form-test',
	templateUrl: 'from-test.component.html',
	styleUrls: ['from-test.component.css']
})
export class FormTestComponent {
	@ViewChild('f') signupForm: NgForm;

	onClick() {
		this.signupForm.patchValue({username: 'suggested name'});
	}
}
```

Per inserire dei valori di default nei campi del form basta usare il property binding su `ngModel`:
```html
<input type="text" id="name" name=username [ngModel]="defaultUsername"/>
```
a volte vogliamo anche agire istantaneamente al cambiamento di un campo, quindi usiamo il two ways binding su `ngModel`.
Per effettuare il reset del form si usa semplicemente il metodo `reset()` sull'oggetto `NgForm`.

##### Validazione
Un'altra cosa importante che si vuole riuscire a fare con i campi di un form è la loro validazione, ossia controllare che i dati inseriti siano validi ed accettabili.  Dato che si sta usando l'approccio template-driven, la validazione va fatta nel template. Per dire che un campo è necessario si aggiunge la direttiva `required`, per dire che un campo deve rispettare la formattazione delle email si usa la direttiva `email`. Inoltre Angular aggiunge dinamicamente delle classi CSS per dare informazioni riguardo la validità dei campi, come `.ng-valid` o `.ng-touched` che permettono di dare styling particolari in base alla validità del form attraverso l'uso della logica CSS.
Inoltre i campi restituiti da `ngForm` permettono di gestire la logica del template e del codice per usare in modo efficiente il form.

##### Grouping Form Controls
Alle volte si vuole che alcuni campi di un form si raggruppino e che magari si possa aggiungere una validazione che valga per tutto il gruppo. Per creare un gruppo di più input bisogna usare la direttiva `ngModelGroup` sul loro contenitore:
```html
<div ngModelGroup="groupName">
	<input ... />
	...
	<input ... />
</div>
```

#### Reactive Approach
Nell'approccio reattivo il form viene creato attraverso il codice TypeScript, quindi bisogna intanto dichiarare una variabile dove questo form verrà salvato:
```ts
@Component({
	selector: 'app-reactive-form',
	templateUrl: 'reactive-form.component.html',
	styleUrls: ['reactive-form.component.css']
})
export class ReactiveFormComponent {
	signupForm: FormGroup;
}
```
inoltre in `app.module.ts` bisogna importare il modulo `ReactiveFormsModule`.
Dopo aver creato la variabile bisogna inizializzare il form attraverso il codice, bisogna avere cura di inizializzare la struttura prima del rendering del component quindi si deve usare un lifecycle hook che viene eseguito prima del rendering.
```ts
@Component({
	selector: 'app-reactive-form',
	templateUrl: 'reactive-form.component.html',
	styleUrls: ['reactive-form.component.css']
})
export class ReactiveFormComponent implements OnInit {
	signupForm: FormGroup;

	ngOnInit(): void {
		this.signupForm = new FormGroup({
			'username': new FormControl('default username'),
			'email': new FormControl('example@example.com'),
			'gender': new FormControl(null)
		});
	}
}
```
bisogna quindi sincronizzare il template HTML con il codice creato in TypeScript. Dobbiamo dire ad Angular di non creare il form in automatico ma di usare il `FormGroup` creato e poi collegare i vari controls con quelli che abbiamo dichiarato:
```html
<form [formGroup]="signupForm">
	<input
		type="text",
		id="username",
		formControlName="username",
		class="form-control"
	/>
</form>
```
in questo caso per effettuare il submit del form si deve usare l'event binding come nel caso del form template driven, solo che non c'è più necessità di passare il riferimento al form in quanto già lo abbiamo nel codice:
```html
<form [formGroup]="signupForm" (ngSubmit)="onSubmit()">
	<input
		type="text",
		id="username",
		formControlName="username",
		class="form-control"
	/>
</form>
```
anche in questo caso vogliamo controllare la validità dei campi del form, come secondo parametro del costruttore `FormControl()` è infatti possibile passare i validatori:
```ts
@Component({
	selector: 'app-reactive-form',
	templateUrl: 'reactive-form.component.html',
	styleUrls: ['reactive-form.component.css']
})
export class ReactiveFormComponent implements OnInit {
	signupForm: FormGroup;

	ngOnInit(): void {
		this.signupForm = new FormGroup({
			'username': new FormControl('default username', Validators.required),
			'email': new FormControl('example@example.com',
			[Validators.required, Validators.email]),
			'gender': new FormControl(null)
		});
	}
}
```
Per accedere facilmente ai controls con questo approccio ai forms si usa il metodo:
```ts
singupForms.get('control-path');
```
nel caso appunto si raggruppino i control dentro dei `FormGroup` bisogna usare il path per raggiungere il control.
```ts
@Component({
	selector: 'app-reactive-form',
	templateUrl: 'reactive-form.component.html',
	styleUrls: ['reactive-form.component.css']
})
export class ReactiveFormComponent implements OnInit {
	signupForm: FormGroup;

	ngOnInit(): void {
		this.signupForm = new FormGroup({
			new FormGroup({
				'username': new FormControl('default username',
					Validators.required),
				'email': new FormControl('example@example.com',
					[Validators.required, Validators.email])}),
			'gender': new FormControl(null)
		});
	}
}
```
in questo caso bisogna specificare la direttiva form group nel tag del contenitore dei controls.

In alcune situazioni si vogliono creare dei form nei quali si hanno degli array di controls, perché si può pensare di volerne aggiungere a piacere.
```ts
@Component({
	selector: 'app-reactive-form',
	templateUrl: 'reactive-form.component.html',
	styleUrls: ['reactive-form.component.css']
})
export class ReactiveFormComponent implements OnInit {
	signupForm: FormGroup;

	ngOnInit(): void {
		this.signupForm = new FormGroup({
			'username': new FormControl('default username', Validators.required),
			'email': new FormControl('example@example.com',
			[Validators.required, Validators.email]),
			'gender': new FormControl(null),

			'hobbies': new FormArray([])
		});
	}

	onAddHobby() {
		const control = new FormControl(null, Validators.required);
		(<FormArray>this.signupForm.get('hobbies')).push(control);
	}
}
```

##### Custom e Asynchronous Validators
Usando il reactive approach si possono creare dei custom validators, cioè delle funzioni a cui viene passato il control e che verificano delle condizioni sul suo valore:
```ts
@Component({
	selector: 'app-reactive-form',
	templateUrl: 'reactive-form.component.html',
	styleUrls: ['reactive-form.component.css']
})
export class ReactiveFormComponent implements OnInit {
	signupForm: FormGroup;
	forbiddenUserNames = ['Chris', 'Mark'];

	ngOnInit(): void {
		this.signupForm = new FormGroup({
			'username': new FormControl('default username',
				[Validators.required, this.forbiddenNames.bind(this)]),
			'email': new FormControl('example@example.com',
				[Validators.required, Validators.email]),
			'gender': new FormControl(null)
		});
	}

	forbiddenNames(control: FormControl): {[s: string]: boolean} {
		if(this.forbiddenUserNames.indexOf(control.value) !== -1) {
			return {'nameIsForbidden': true};
		}
		return null;
	}
}
```
non sempre però si ha immediatamente la validità o meno di un campo, alle volte è necessario raggiungere un server web prima di poterlo sapere, in questo caso però si tratta di un'operazione asincrona perché il risultato non arriva immediatamente ma ha bisogno di tempo, anche in questo caso si deve creare una funzione che però restituisce una `Promise` o un `Observable`.
```ts
@Component({
	selector: 'app-reactive-form',
	templateUrl: 'reactive-form.component.html',
	styleUrls: ['reactive-form.component.css']
})
export class ReactiveFormComponent implements OnInit {
	signupForm: FormGroup;

	ngOnInit(): void {
		this.signupForm = new FormGroup({
			'username': new FormControl('default username', Validators.required),
			'email': new FormControl('example@example.com',
			[Validators.required, Validators.email, forbiddenEmails]),
			'gender': new FormControl(null)
		});
	}

	forbiddenEmails(control: FormControl): Promise<any> | Observable<any>{
		const promise = new Promise<any>((resolve, reject) => {
			if(control.value === /*somewhere stored value*/)
				resolve({'emailIsForbidden': true});
			resolve(null);
		}); 
	}
}
```
nell'attesa che la promessa venga risolta Angular aggiunge la classe `ng-pending` al control.