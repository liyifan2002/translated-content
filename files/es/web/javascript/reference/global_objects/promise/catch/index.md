---
title: Promise.prototype.catch()
slug: Web/JavaScript/Reference/Global_Objects/Promise/catch
translation_of: Web/JavaScript/Reference/Global_Objects/Promise/catch
original_slug: Web/JavaScript/Referencia/Objetos_globales/Promise/catch
---
{{JSRef}}

El método **catch()** retorna una `Promise` y solo se ejecuta en los casos en los que la promesa se marca como `Reject`. Se comporta igual que al llamar {{jsxref("Promise.then", "Promise.prototype.then(undefined, onRejected)")}} (de hecho, al llamar `obj.catch(onRejected)` internamente llama a `obj.then(undefined, onRejected)`).

## Síntaxis

    p.catch(onRejected);

    p.catch(function(reason) {
       // rejection
    });

### Parámetros

- onRejected
  - : Una {{jsxref("Function")}} llamada cuando la `Promise` es rechazada. Esta función tiene un argumento:_ `reason`
    _ : La razón del rechazo.La promesa devuelta por `catch()` es rechazada si `onRejected` lanza un error o retorna una `Promise` que a su vez se rechaza, de cualquier otra manera la `Promise` es resuelta.

### Valor de Retorno (Return)

Internamente llama a `Promise.prototype.then` en el objeto sobre el que se llama, pasándole el parámetro `undefined` y el manejador `onRejected` recibido; luego devuelve un valor de esa llamada (que es una {{jsxref("Promise")}}).

**Demostración de la llamada interna:**

```js
// overriding original Promise.prototype.then/catch just to add some logs
(function(Promise){
    var originalThen = Promise.prototype.then;
    var originalCatch = Promise.prototype.catch;

    Promise.prototype.then = function(){
        console.log('> > > > > > called .then on %o with arguments: %o', this, arguments);
        return originalThen.apply(this, arguments);
    };
    Promise.prototype.catch = function(){
        console.log('> > > > > > called .catch on %o with arguments: %o', this, arguments);
        return originalCatch.apply(this, arguments);
    };

})(this.Promise);



// calling catch on an already resolved promise
Promise.resolve().catch(function XXX(){});

// logs:
// > > > > > > called .catch on Promise{} with arguments: Arguments{1} [0: function XXX()]
// > > > > > > called .then on Promise{} with arguments: Arguments{2} [0: undefined, 1: function XXX()]
```

## Descripción

El método `catch` puede ser muy útil para el manejo de errores en tu código con promesas.

## Ejemplos

### Usando y encadenando el método `catch`

```js
var p1 = new Promise(function(resolve, reject) {
  resolve('Success');
});

p1.then(function(value) {
  console.log(value); // "Success!"
  throw 'oh, no!';
}).catch(function(e) {
  console.log(e); // "oh, no!"
}).then(function(){
  console.log('after a catch the chain is restored');
}, function () {
  console.log('Not fired due to the catch');
});

// The following behaves the same as above
p1.then(function(value) {
  console.log(value); // "Success!"
  return Promise.reject('oh, no!');
}).catch(function(e) {
  console.log(e); // "oh, no!"
}).then(function(){
  console.log('after a catch the chain is restored');
}, function () {
  console.log('Not fired due to the catch');
});
```

### Trucos cuando lanzamos errores

```js
// Hacer un throw llamará al método catch
var p1 = new Promise(function(resolve, reject) {
  throw 'Uh-oh!';
});

p1.catch(function(e) {
  console.log(e); // "Uh-oh!"
});

// Los errores que se lancen dentro de funciones asíncronas actuarán como errores no capturados
var p2 = new Promise(function(resolve, reject) {
  setTimeout(function() {
    throw 'Uncaught Exception!';
  }, 1000);
});

p2.catch(function(e) {
  console.log(e); // Nunca será llamado
});

// Errores lanzados después de resolve() serán omitidos
var p3 = new Promise(function(resolve, reject) {
  resolve();
  throw 'Silenced Exception!';
});

p3.catch(function(e) {
   console.log(e); // Nunca será llamado
});
```

### Si se resuelve la promesa

```js
// Crea una promesa que no llamará a onReject
var p1 = Promise.resolve("calling next");

var p2 = p1.catch(function (reason) {
    // Nunca será llamado
    console.log("catch p1!");
    console.log(reason);
});

p2.then(function (value) {
    console.log("next promise's onFulfilled"); /* next promise's onFulfilled */
    console.log(value); /* calling next */
}, function (reason) {
    console.log("next promise's onRejected");
    console.log(reason);
});
```

## Especificación

| Specification                                                                                                | Status                       | Comment                                |
| ------------------------------------------------------------------------------------------------------------ | ---------------------------- | -------------------------------------- |
| {{SpecName('ES2015', '#sec-promise.prototype.catch', 'Promise.prototype.catch')}} | {{Spec2('ES2015')}}     | Definición inicial en el standar ECMA. |
| {{SpecName('ESDraft', '#sec-promise.prototype.catch', 'Promise.prototype.catch')}} | {{Spec2('ESDraft')}} |                                        |

## Compatibilidad de navegadores

{{Compat("javascript.builtins.promise.catch")}}

## Vea también

- {{jsxref("Promise")}}
- {{jsxref("Promise.prototype.then()")}}