For a long time, JavaScript had no way to define private class fields. By private we mean only accessible from inside the class and not accessible from the outside. So, developers were "simulating" private instance variables and instance methods by prefixing them with an `_` (underscore character):

```js
class User {
  constructor() {
    this._votingAge = 18; // meant as private
  }
}
```

Enter private class fields.

## Private instance variables

To mark an instance variable or method as private, you have to prefix it with the # (hash sign). For example:

```js
class User {
  #votingAge = 18;
}
```

Now, #votingAge is a private class field that cannot be accessed from outside of the class. It can only be accessed from inside the class with this.#votingAge.

Note that private class fields have to be defined outside of the constructor first. Then, you can decide to re-use them in the constructor. For example, the code below is invalid:

```js
class User {
  constructor() {
    // ❌ this does NOT work
    this.#votingAge = 18;
  }
}
```

## Private instance methods

Private instance methods are often denoted as "helper" methods that are only meant to be used from inside the class. Since there was no JavaScript feature that marked instance methods as private, developers were also prefixing them with an \_.

However, now with private class fields, you can prefix the method with # and it won't be accessible from the outside:

```js
class User {
  constructor(age) {
    this.age = age;
    this.#logAge();
  }

  #logAge() {
    console.log(this.age);
  }
}
```

const user = new User(20);
// cannot call user.#logAge() or user.logAge()

The #logAge private method cannot be called from outside of the class. It can only be called from the inside. So, the this.#logAge() inside the constructor does work indeed but you will not be able to call #logAge() on an instance variable.

#############################################################################

event
