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
