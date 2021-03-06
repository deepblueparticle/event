Event
=====

Event is a header only library that makes it easy to create event driven
programs in C++11.


Usage
-----

Simply include event.hpp, there are no dependencies (other than a compiler that
supports the memory and functional C++11 standard library components).

Create an event by using the Event template class:
```cpp
Event<> my_event;
```

Events can have none or many arguments of any type you would pass in to a
function:
```cpp
Event<int, int&, const int&, int*, CustomClass> my_event;
```

Firing an Event will synchronously execute all bound functions. Firing an Event
requires supplying the arguments specified in the template. These values will
be passed in to the bound functions.
```cpp
Event<int> my_event;
my_event.fire(0);
my_event.fire(1);
```

Functions that take the same arguments and return void may be bound to the 
Event. A bound function will automatically be unbound when the bind (
std::shared_ptr&lt;Event&lt;Args...&gt;::Bind&gt;) falls out of scope. Binds
can safely outlive the respective Event. Alternatively a function can be
permanently bound using the Event::permanent_bind method.
```cpp
Event<int> my_event;
{
	// bind for the duration of the bind object
	auto bind = my_event.bind([](int input){
		std::cout << "temporary:" << input << std::endl;
	});
	// permanently bind
	my_event.permanent_bind([](int input){
		std::cout << "permanent: " << input << std::endl;
	});
	// fire, both binds will execute
	my_event.fire(0);
}
// fire, only the permanent bind will execute as the temporary bind has fallen
// out of scope
my_event.fire(1);
```

The above code will output:
````
temporary: 0
permanent: 0
permanent: 1
````

It is also safe to bind and unbind in a function that is bound to an Event,
even while that Event is firing:
```cpp
std::shared_ptr<Event<>::Bind> bind = 0;
Event<> my_event;
// bind a function that automatically unbinds itself after executing once
bind = my_event.bind([&bind](){
	std::cout << "hello world" << std::endl;
	bind = 0;
});
my_event.fire();
my_event.fire();
```

The above code will output:
````
hello world
````


Test
-----
Tests are successful if there is no output. Example build command with gcc on
windows:
````
g++ -ggdb -Wall --std=c++11 test.cpp -o test.exe
````