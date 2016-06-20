# About #

µTest is an ultra-lightweight, minimalist unit test framework for C++11. It is the sister-framework of [µTest for C99](https://github.com/evolutional/utest).


## Compiling ##

µTest is provided as a single-header library. To compile, you simply include the header where you need to and ensure that you provide the implementation by defining `UTEST_CPP_IMPLEMENTATION` in **exactly one** source file before you include the header.

		#define UTEST_CPP_IMPLEMENTATION
		#include "upptest.h"

There are no external dependencies required, there are no special build processes or requirements.

# Usage #

## Implementing Tests ##

This is a simple example of how to implement a basic test in µTest.

	TEST(ExampleFailingEqualityTest, "Example.Tests")
	{
		utest::assert::neq(50, 50);
	}

Each test is declared with a name ('ExampleFailingEqualityTest') and a category ('Example.Tests').

Tests follow the xUnit pattern, with each test being encapsulated in its own class. When declaring tests, it is recommended you declare them in a `.cpp` file as they will automatically register themselves with the engine.

## Assertion ##

µTest provides several simple assertion template functions for use in tests. Is an assert condition fails, a test will terminate immediately, throwing a `utest::assert_fail_exception` which is handled by the execution engine.

Example:

	utest::assert::eq(50, 50);

The basic functions are:

	assert::eq
	assert::neq
	assert::is_true
	assert::is_false
	assert::is_null
	assert::is_not_null
	assert::fail
 
## Executing Tests ##

Executing the tests can be performed in several ways. The simplest is to run all tests that are registered:

	auto res = utest::runner::run_registered([](const auto& tst){
		// observer fired after completion
		// you have full info about the test here
		// including status, error message, execution time, etc
	});

µTest features no built-in test result reporters, instead you provide your own observer function that is called after each test has been executed.

An example that runs all test and prints the results to `std::cout` is:

	auto res = utest::runner::run_registered([](const utest::result& tst){
		std::cout << "'" << tst.info->name << "' executed in "
			<< tst.duration.count() << "ms with result: ";
		if (tst.status == utest::status::pass)
			std::cout << "pass" << std::endl;
		else
			std::cout << "failed" << std::endl;
	});

### Filtering ###

Tests can be executed with a filter predicate which will be passed a `const utest::info const*` for evaluation. 

An example which filters tests in a specific category:

	auto res = utest::runner::run_registered([](auto ti) {
		return std::string(ti->category) == "Example.Tests"; 
		},	
		[](const auto&) {}
	);

All tests are registered with a global `utest::registry`.  You can retrieve these tests by accessing:

	const auto& tests = utest::registry::get()->tests();

You can use this to fill your own stl containers and then execute them with `utest::runner::run()`. The run function has various overrides that accepts collections, begin/end range iterators and filter predicates, allowing you to easily create your own filtering and execution process.

## Fixtures ##

µTest supports fixtures by allowing tests to share a common base class and category. This allows you to implement complex tests that share common logic.

Fixtures are declared as follows:

	TEST_FIXTURE(MyFixture)
	{
	public:
		MyFixture()
			: expected(100)
		{		
		}

		int expected;
	};

There's nothing special about the macro, it simply declares a class that inherits `utest::test`.

From here, you declare fixture tests as follows:

	TEST_F(PassingEqualityTest, MyFixture, "MyFixtures.Tests")
	{
		utest::assert::eq(expected, 100);
	}


## FAQ ##

*Will µTest support feature X from {insert popular library}?*

Highly unlikely. µTest created with minimalism in mind. However, if you find yourself implementing a useful, core feature feel free to contribute a pull request.

*Why should I use µTest over {insert popular library}?*

µTest was born out of a need to embed a tiny test framework in a project and disable it with a preprocessor define. 

*Why public domain?*

See [stb's FAQ](https://github.com/nothings/stb) for a great set of reasons. µTest is completely free for you to use in any way you see fit. Attribution is not expected, but it is appreciated.

*What parts of C++11 does µTest use?*

`std::chrono`, `std::memory`, `std::functional`.

*What's the future for µTest?*

µTest is essentially feature complete. It may be updated with a richer set of assert functionality should the need arise.

## License ##

µTest is free and unencumbered software released into the public domain. See UNLICENSE for details.
