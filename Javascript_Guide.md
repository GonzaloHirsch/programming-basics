# Javascript Guide

## Objects
Referencia:[Fun Fun Functions](https://www.youtube.com/playlist?list=PL0zVEGEvSaeHBZFy6Q8731rcwk0Gtuxub)

Se puede definir un objeto haciendo un `let` en una variable.

```
let obj_name = {
	propertyt: value,
	fun_name: function(){
		//do smth
	}
}

obj_name.fun_name()
```

Hay que tener en cuenta que se puede acceder a las propiedades y funciones del objeto como se hace normalmente.

**Nota**: Si la función hace referencia a *this*, al asignarlo a una variable fuera del contexto del objeto, tira ***undefined***.
Colisiona la parte funcional con la orientada a objetos, para resolver esto hay que hacer un *bind* (fuerza a que use el contexto del objeto que se le pasa como parámetro):

```
let aux_fun = obj_name.fun_name()
let bound_aux_fun = aux_fun.bind(obj_name)
bound_aux_fun()
```

Si asignas una funcion que usa *this* a un objeto, al llamarse toma el contexto del objeto solo (sin necesidad del bind).

```
function fun(){
	console.log(this.prop)
}

let obj_name = {
	prop: "Prop",
	fun_prop: fun
}

obj_name.fun_prop() //"Prop"
fun() //"Undefined"

```

### Prototypes

Los objetos heredan propiedades y métodos de los prototipos.

Se puede asignar un prototipo a un objeto, y eso simula una pseudo herencia, se hace con un `Object.setPrototypeOf(obj, prototype)`:

```
function fun(){
	console.log(this.sound)
}

let proto = {
	fun_call: fun
}

let obj_name = {
	sound: "Boop!"
}

Object.setPrototypeOf(obj_name, proto)
obj_name.fun_call() //"Boop!"
```

**Nota**: El objeto en si, sigue siendo el objeto/contexto al que se le asigna el prototipo, **no** el prototipo.

## New
Genera un nuevo objeto como al que se le llamó el *new*.

Toma el prototipo del nuevo objeto y le pone el mismo prototipo que el del objeto que recibe el *new*.

Devuelve el nuevo objeto.

## Objects in ECMAScript 5

No existía la keyword *class*, entonces se hace algo para "falsificar" la palabra *new*.

Se hace lo siguiente:

```
function Obj(prop){
	this.prop = prop
}

Obj.prototype.fun = function(){
	console.log(this.prop)
}

var obj_instance = new Obj("Prop Value")
obj_instance.fun() //"Prop Value"
```
## Prototype vs \_\_proto\_\_
Cada objeto tiene un prototipo al que delega.

Este protipo, si no especificado con *Object.setPrototypeOf*, es como el Object global del que todos delegan.

**\_\_proto\_\_**: Devuelve el prototipo al que están delegando
**prototype**: Devuelve el prototipo que tiene

**Nota**: **Todos** los objetos tienen un prototipo que delegan, entonces siempre va a devolver algo \_\_proto\_\_, mientras que las funciones solo tienen prototype a menos que se use *Object.setPrototypeOf* o un *new*.

## Object.create()
Existe el *create* como una forma más natural de crear objetos que el *new*.

Crea un nuevo objeto con el objeto que se pasa como parámetro como prototipo.

```
let new_obj = Object.create(other_obj)

other_obj.isPrototypeOf(new_obj) //Devuelve true
```

Hay que definir un método *init* en el objeto que se usa como prototipo, el *init* sirve como un constructor. Este *init* debería devolver al objeto para que se puedan encadenar las llamadas de esta forma.

```
const obj = {
	init: function(param) {
		this.param = param
		return this
	},
	fun: function() {
		// Do smth
	}
}

const obj_instance = Object.create(obj).init(param)
```

## Class
**Usar esta forma para crear clases**

Existe la keyword *class*, y se usa de la siguiente forma.

```
class MyClass {
	constructor(param){
		this.param = param
	}

	fun(){
		// Do smth
	}
}

let obj = new MyClass(param) 
```

**Nota**: El *constructor* es el cosntructor que recibe los parámetros.

**Consideraciones**:
* No se puede poner la visibilidad de una propiedad, se usa "\_" para indicar que son privadas

### Herencia
Existe herencia, se usa la keyword *extends*.

```
class Parent {
	constructor(param){
		this.param = param
	}
	
	fun(){
		// Do smth
	}
}

class Child extends Parent{
	constructor(){
		super(param)
	}
}
```

**Nota**: Se usa *super* para llamar al constructor de la clase de la que uno hereda.

## Asignación de Variables

Se puede asignar una funci