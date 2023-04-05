Le funzioni pipe permettono di trasformare stringhe, prezzi, date ed altri dati in base alla situazione ed al contesto locale, si tratta di semplici funzioni che prendono un dato dal template e lo restituiscono trasformato. Sono utilizzabili nell'intera applicazione dichiarandole una volta sola.
Per utilizzare una funzione pipe all'interno del template si utilizza il carattere pipe `|`, come nell'esempio:
```html
<p> Today is {{ todayDate | date }} </p>
```
alcune funzioni pipe accettano parametri che in questo caso vengono passati utilizzando il carattere colon `:` come nell'esempio:
```html
<p> {{ '100' | currency:'EUR':'Euros ' }} <p>
```
si possono anche concatenare le pipes ottenendo formattazioni più complesse:
```html
<p> {{ currentDate | date | uppercase }} </p>
```

#### Custom Pipes
A volte si hanno delle necessità non coperte dalle formattazioni possibili date dalle pipes, per creare una pipe bisogna creare un file `name.pipe.ts` dove si esporta una classe:
```ts
@Pipe({
	name: 'shorten'
})
export class ShortenPipe implements PipeTransform {
	transform(value: any) {
		return value.substr(0, 5);
	}
}
```
successivamente si deve aggiungere la pipe nella sezione `Declaration` di `app.module.ts`.
Si possono anche parametrizzare le pipe personalizzate:
```ts
@Pipe({
	name: 'shorten'
})
export class ShortenPipe implements PipeTransform {
	transform(value: any, limit: number, anArg: any) {
		return value.substr(0, limit);
	}
}
```
Le pipe non vengono rieseguite ogni volta che cambiano i dati, a meno che non venga aggiunta la proprietà al decoratore:
```ts
@Pipe({
	name: 'shorten',
	pure: 'false'
})
```
però può portare a problemi di performance.

#### Async Pipe
Se si ha una `Promise` o un `Observable` come dato che si vuole interpolare, non è possibile farlo direttamente perché non verrebbe aggiornato automaticamente quando arriva, ma usando la pipe `async` sarà possibile farlo.