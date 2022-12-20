# laravel-interface
What is a Laravel Interface?

In Laravel, an interface is a contract for what methods will be used in a specific class. Actually, interfaces are not specific to Laravel, or even native PHP for that matter. Since we’ve been on a roll with Laravel, we’ll talk about interfaces in Laravel. Let’s investigate what interfaces are and why we might want to use them, let’s jump in!


When would I want to use an Inteface?
Let’s remember our Inversion of Control article in which we examined how and why the IoC container in Laravel is a great thing. We had a Car in that example. Over time, maybe we built a large application that makes heavy use of that Car class, and our code relies on that class in many places throughout the application. Well guess what, you just got a huge promotion, and soon enough you are going to be driving a new Porche. You need to swap out that Car class with the new Porche. You’ll have to dig through all the source code and try to figure out how the API of that class works. You might get it right, you might not. Well, that sucks! In a case like this, an Interface would have saved you a lot of time and effort!

How do we create an Interface?
Creating an Interface is super easy. It looks just like a normal class but instead we use the interface keyword. Instead of creating a Car class, or a Subaru class, or a Porche class, we can first begin by creating a CarInterface:

Create the Interface
```php
interface CarInterface {
    public function start();
    public function gas();
    public function brake();
}
```

Awesome! This tells our code that we can have any type of car we want, as long as it implements a start() gas() and brake() method. This is the API or contract of the interface.

Create a Class that Implements the Interface
This is also quite easy. All we have to do is create a class as we normally would, but we need to make sure we add the implements keyword to our definition:

```php
class Subaru implements CarInterface {
    public function start() {
        echo 'Starts great';
    }
    public function gas()  {
        echo 'Hit the gas and let the all wheel drive grip those back roads!';
    }
    public function brake() {
        echo 'Wow these Brembo brakes are powerful';
    }
}
```

Start Using Your Interface!
Oh man that was so cool – we set up our CarInterace and we have a class that implements it, we’re future proofing our application and making sure we have testability baked in as well since we can so easily swap instances in and out of our app. Let’s test this puppy out:

```php
Route::get('/', function()
{
    dd(App::make('CarInterface'));  
});
```

Ut Oh, Debbie Downer on the scene. Whoops, there was an error! Illuminate Container BindingResolutionException Target [CarInterface] is not instantiable. Ok, we made a boo boo. It looks like we can’t directly instantiate an interface. Oh yes! We need to bind it first!

Bind Your Interface
We can easily bind our interface to the app like by adding App::bind('CarInterface', 'Subaru'); into the code. Let’s see the whole thing in action:

```php
interface CarInterface {
    public function start();
    public function gas();
    public function brake();
}

class Subaru implements CarInterface {
    public function start() {
        echo 'Starts great'.'<br>';
    }
    public function gas()  {
        echo 'Hit the gas and let the all wheel drive grip those back roads!'.'<br>';
    }
    public function brake() {
        echo 'Wow these Brembo brakes are powerful'.'<br>';
    }
}

App::bind('CarInterface', 'Subaru');
 
Route::get('/', function()
{
    $car = App::make('CarInterface');  
	$car->start();
	$car->gas();
	$car->brake();
});
//  Starts great
//  Hit the gas and let the all wheel drive grip those back roads!
//  Wow these Brembo brakes are powerful
```

Excellent. Here, we create a CarInterface, define a Subaru class based on that interface, bind it to our app, and then use it anywhere we want in our code.

Swap Implementations
We’ve done a great job, and for our hard work, we do get that promotion. With it, comes a new Porche and we want to make use of it right away. Let’s do so:

```php
interface CarInterface {
    public function start();
    public function gas();
    public function brake();
}

class Porche implements CarInterface {
    public function start() {
        echo 'Push button start'.'<br>';
    }
    public function gas()  {
        echo '0 to 60 in 3.7 seconds!'.'<br>';
    }
    public function brake() {
        echo 'Brakes? Who needs brakes?'.'<br>';
    }
}

App::bind('CarInterface', 'Porche');
 
Route::get('/', function()
{
    $car = App::make('CarInterface');  
	$car->start();
	$car->gas();
	$car->brake();
});
//  Push button start
//  0 to 60 in 3.7 seconds!
//  Brakes? Who needs brakes?
```

Speed. I am Speed. Nice – you got your Porche and it is working great thanks to our contract by way of the CarInterface we had set up initially.

Note: You can also inject these directly into your controllers like so:

```php
class CarController extends BaseController {
	public function __construct(CarInterface $car)
	{
		$this->$car = $car;
	}
}
```

Whatever is bound to the interface is the class you will get access to.
