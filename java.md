- All primitive types in java are lowercase

* java class library has helper class for each primitive type. Helper class support conversion and formatting tools:

  <!-- | Data type | Helper Class |
  | --------- | ------------ |
  | byte | Byte |
  | short | Short |
  | int | Integer |
  | float | Float | -->

* all primitive numeric variables default to 0.

#############################################

There are two ways to recive input from CLI

1st method

return System.console().readLine("Enter name");

this does't work in intellj idea, so we use next:

2nd method

Scanner scanner = new Scanner(System.in);
System.out.println("Enter name: ");
String name = scanner.nextLine();

#############################################

Access modifiers Java:

There are four types of Java access modifiers:

Private: The access level of a private modifier is only within the class. It cannot be accessed from outside the class.

Default: The access level of a default modifier is only within the package. It cannot be accessed from outside the package. If you do not specify any access level, it will be the default.

Protected: The access level of a protected modifier is within the package and outside the package through child class. If you do not make the child class, it cannot be accessed from outside the package.

Public: The access level of a public modifier is everywhere. It can be accessed from within the class, outside the class, within the package and outside the package.

src: https://www.javatpoint.com/access-modifiers

#############################################

No-arg default constructor is only defiend when you have not created any constructor yourself.

#############################################

Fields with primitive types can't be assigned null values

#############################################

POJO - Plain old Java Objects. Classes which only contains instance fiels a a few instance methods are called POJO.

here is an example:

```java
public class Student {
    private String id;
    private String name;

    private String dob;

    private String classList;

    public Student(String id, String name, String dob, String classList) {
        this.id = id;
        this.name = name;
        this.dob = dob;
        this.classList = classList;
    }

    @Override
    public String toString() {
        return super.toString();
    }
}
```

Creating POJO requires lot of biolerplate code this is why we use Record type. The Record is a special class that contains the data , that is nto meant to be altered. It only contains fundamental methods such as constructors and accessors.

Here is an examle of Record types:

```java
public record LPAStudent(String id, String name, String dob, String classList) {
}
```

################################################
