# Android Promiser

A lightweight implementation of [Promises/A+](https://promisesaplus.com/) specification.

## Gradle

Add the following dependency to ```build.gradle```:

```
dependencies {
    ...
    compile 'com.octopepper.promiser:android-promiser:1.0.2'
}
```

## Requirements

This library is written using Java 8 syntax.

Let's embrace the future ! 😄

To use this into Java 7, 6 and 5 projects, don't forget to install [retrolambda](https://github.com/orfjackal/retrolambda) by [@orfjackal](https://github.com/orfjackal).

## Usage

You can create a Promiser object like this :

```java
Promiser<T, U> p = new Promiser<T, U>(
    (Resolver<T> resolve, Rejecter<U> reject) -> {

        // Place your asynchronous process here, and make sure
        // to trigger resolve.run() or reject.run() when needed.

});
```

```<T>``` is the type of the result returned in case of success and ```<U>``` is the type of the result returned in case of error.

You can handle result and error cases like this now :

```java
p.success((T result) -> {
      // Handle success here

    }).error((U err) -> {
      // Handle error here

    });
```

If you want to do multiple asynchronous tasks sequentially use ```.then()```

```java
p.then((T result) -> {
      // T instanceOf String
      // example X = parseToInt(T)

    }).then((X result) -> {
      // Now X instanceOf int

    });
```

## Example

For example let's mock an asynchronous process using [**Retrofit v2**](http://square.github.io/retrofit/)

```java
public Promiser<String, Integer> fetchUsers() {
        return new Promiser<>((resolve, reject) -> {
            retrofit.getService(IUserService.class).fetchUsers().enqueue(new Callback<String>() {
                @Override
                public void onResponse(Response<String> response) {
                    if (response.isSuccess())
                        resolve.run(response.body()); //resolving result
                    else
                        reject.run(response.code()); //rejecting error code
                }

                @Override
                public void onFailure(Throwable t) {
                    reject.run(CodeError.Undefined.getCode()); //reject error
                }
            });
        });
}
```
Now we can handle the success or the error of this promise using the ```.success()``` and ```.error()``` callbacks :

```java
fetchUsers()
    .success(str -> {
      // Handle success here

    }).error(code -> {
      // Handle error here

    });
```

```java
fetchUsers()
    .then(str -> {
      // Parse Json to Object

    }).then(users -> {
      // Do something

    });
```

or even better:
```java
fetchUsers()
    .success(this::resultSucceeded)
    .error(this::resultError);

private void resultSucceeded(String s) {
  // Handle success here

}

private void resultError(Integer code) {
  // Handle error here

}
```

## Next step

* Make ```.then()``` accept a Promiser as a return type. The Promiser returned will have to be resolved.

```java
fetchUser()
    .then(user -> fetchPhotosByUser())
    .then(photos -> {
      // Do something

    });

```


## Contributors

[NodensN](https://github.com/NodensN),
[YannickDot](https://github.com/YannickDot),
[s4cha](https://github.com/s4cha)
