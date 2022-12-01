# About Routes
- **All Laravel routes are defined in your route files, which are located in the routes directory.**
- **These files are automatically loaded by your application's App\Providers\RouteServiceProvider.**

# Available Router Methods
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);

## Return Value | Basic Routing
# url => http://127.0.0.1:8000/greeting => hello world
Route::get('/greeting', function () {
    return 'hello world';
});

## Match Route
# url => http://127.0.0.1:8000/match => get or post
Route::match(['get', 'post'], '/match', function () {
    return 'get or post';
});

## Any Route
# url => http://127.0.0.1:8000/match => get or post
Route::any('/any-route', function () {
    return 'any method can hit';
});

## Dependency Injection
# url => http://127.0.0.1:8000/users?name=Muhammad%20Raza%20Bangi
Route::get('/users', function (Request $request) {
    dd($request->all()); // ['name' => 'Muhammad Raza Bangi']
});

## Redirect Routes
Route::get('/there', function () {
    return 'there';
});
# url => http://127.0.0.1:8000/here if you hit here it will redirect to there route
// Route::redirect('/here', '/there');
// Route::redirect('/here', '/there', 301);

// Or, you may use the Route::permanentRedirect method to return a 301 status code:
Route::permanentRedirect('/here', '/there', 301);

## Route Parameters
# url => http://127.0.0.1:8000/user/2
Route::get('/user/{id}', function ($id) {
    return 'User '.$id; // User 2
});

## multi parameters
# url => http://127.0.0.1:8000/posts/2/comments/3
Route::get('/posts/{post}/comments/{comment}', function ($postId, $commentId) {
    return 'Post ' . $postId . ' Comment: ' . $commentId; //Post 2 Comment: 3
});

## Optional Parameters
// if you hit http://127.0.0.1:8000/fullname/rizwan result will be rizwan
// if you hit http://127.0.0.1:8000/fullname result will be Muhammad Raza Bangi
Route::get('/fullname/{name?}', function ($name = 'Muhammad Raza Bangi') {
    return $name;
});

## Regular Expression Constraints
// if you hit http://127.0.0.1:8000/bio/raza then result will be "raza"
// if you hit http://127.0.0.1:8000/bio/456 then result will be 404 because of patterns
Route::get('/bio/{name}', function ($name) {
    return $name;
})->where('name', '[A-Za-z]+');

// if you hit http://127.0.0.1:8000/number/raza then result will be 404
// if you hit http://127.0.0.1:8000/number/456 then result will be 456 because of patterns
Route::get('number/{number}', function ($number) {
    return $number;
})->where('number', '[0-9]+');

// if you hit http://127.0.0.1:8000/info/12/raza then result will be raza-12
// if you hit http://127.0.0.1:8000/info/raza/12 then result will be 404
// if you hit http://127.0.0.1:8000/info/raza/raza then result will be 404
// if you hit http://127.0.0.1:8000/info/12/12 then result will be 404
Route::get('info/{id}/{name}', function ($id, $name) {
    return $name . '-' . $id;
})->where([
    'id' => '[0-9]+',
    'name' => '[A-Za-z]+'
]);

## Regular expression patterns have helper methods
// if you hit http://127.0.0.1:8000/getName/raza then result will be raza
// if you hit http://127.0.0.1:8000/getName/12 then result will be 404
Route::get('getName/{name}', function ($fullname) {
    return $fullname;
})->whereAlpha('name');

// if you hit http://127.0.0.1:8000/getNumber/123 then result will be 123
// if you hit http://127.0.0.1:8000/getNumber/raza then result will be 404
Route::get('getNumber/{number}', function ($number) {
    return $number;
})->whereNumber('number');

// if you hit http://127.0.0.1:8000/getBio/123/raza then result will be raza | 123
// if you hit http://127.0.0.1:8000/getBio/123/123 then result will be 404
// if you hit http://127.0.0.1:8000/getBio/raza/raza then result will be 404
Route::get('getBio/{number}/{name}', function ($number, $name) {
    return $name . ' | ' . $number;
})->whereAlpha('name')->whereNumber('number');

// if you hit http://127.0.0.1:8000/getUuid/fca08d6a-9946-48dc-a7da-e50ce8e118d5 then result will be "fca08d6a-9946-48dc-a7da-e50ce8e118d5"
// if you hit http://127.0.0.1:8000/getUuid/fca08d6a-9946-48dc-a7da then result will be 404
Route::get('getUuid/{id}', function ($id) {
    return $id;
})->whereUuid('id');

// if you hit http://127.0.0.1:8000/categories/software then result will be "software"
// if you hit http://127.0.0.1:8000/categories/health then result will be 404
Route::get('categories/{category}', function ($category) {
    return $category;
})->whereIn('category', ['hardware', 'software']);

## Global Constraints
// If you would like a route parameter to always be constrained by a given regular expression,
// you may use the pattern method. You should define these patterns in the boot method of your
// App\Providers\RouteServiceProvider class:
// public function boot()
// {
//     Route::pattern('id', '[0-9]+');
// }

// if you hit http://127.0.0.1:8000/id/12 then result will be 12
// if you hit http://127.0.0.1:8000/id/raza then result will be 404
// after add pattern on boot method in routeserviceprovider class it will globally set
Route::get('id/{myid}', function ($myid) {
    // Only executed if {myid} is numeric...
    return $myid;
});

## Encoded Forward Slashes
// by this you can show multiple slashes values like /search/raza/12/engineer
// if you hit http://127.0.0.1:8000/search then result will be 404
// if you hit http://127.0.0.1:8000/search/raza then result will be raza
// if you hit http://127.0.0.1:8000/search/raza/bangi then result will be raza/bangi
Route::get('/search/{search}', function ($search) {
    return $search;
})->where('search', '.*');

## Named Routes
// now this url short name is profile
Route::get('user/profile', function () {
    return 'my profile';
})->name('profile');

// if you hit http://127.0.0.1:8000/getProfile then it will redirect to profile named route which is user/profile
Route::middleware('myroute')->get('getProfile', function () {
    // dd(route('profile')); => http://127.0.0.1:8000/user/profile
    // return redirect()->route('profile'); // long way
    return to_route('profile'); // short way to redirect the profile
})->name('another-profile');

// you may pass the parameters as the second argument to the route function
Route::get('user/{id}/comment', function ($id) {
    return 'my id is: ' . $id . ' and the comment is not available';
})->name('comment');

// if you hit http://127.0.0.1:8000/getComment then
// it will redirect to comment named route with parameters which is user/{id}/comment
Route::get('getComment', function () {
    return to_route('comment', ['id' => 16]);
});

// If you pass additional parameters in the array,
// those key / value pairs will automatically be added to the generated URL's query string:
Route::get('getComments', function () {
    return to_route('comment', ['id' => 12, 'name' => 'raza']);
});

## Inspecting The Current Route
// If you would like to determine if the current request was routed to a given named route,
// you may use the named method on a Route instance. For example,
// you may check the current route name from a route middleware:
// -> make middleware and check the named route
// public function handle($request, Closure $next)
// {
//     if ($request->route()->named('profile')) {
//         //
//     }
//     return $next($request);
// }


## Route Group
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/getname', function () {
        return 'get your name';
    });
    Route::get('/getnumber', function () {
        return 'get your number';
    });
});

## Route Prefixes
# url => if you hit http://127.0.0.1:8000/admin/profile then the result wil be admin wala profile...
Route::prefix('admin')->group(function () {
    Route::get('profile', function () {
        return 'admin wala profile...';
    });
});

## Route Name Prefixes
Route::prefix('admin')->name('admin.')->group(function () {
    Route::get('dashboard', function () {
        return 'admin dasboard..';
    })->name('dashboard');
});

## Implicit Binding
// use App\Models\User;

Route::get('/users/{user}', function (User $user) {
    return $user->email;
});


// Soft Deleted Models
// use App\Models\User;

// Route::get('/users/{user}', function (User $user) {
//     return $user->email;
// })->withTrashed();

// Customizing The Key
// use App\Models\Post;

Route::get('/posts/{post:slug}', function (Post $post) {
    return $post;
});

## Always get another key instead of id
// If you would like model binding to always use a database column other than id when retrieving a given model class,
// you may override the getRouteKeyName method on the Eloquent model:
// public function getRouteKeyName()
// {
//     return 'slug';
// }

## Custom Keys & Scoping
// use App\Models\Post;
// use App\Models\User;

Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
    return $post;
});

## Custom Keys & Scoping
// use App\Models\Post;
// use App\Models\User;

Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
    return $post;
});

## Scope Binding

// use App\Models\Post;
// use App\Models\User;

Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
    return $post;
})->scopeBindings();

// or

Route::scopeBindings()->group(function () {
    Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
        return $post;
    });
});

## Without Scope Binding
Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
    return $post;
})->withoutScopedBindings();

## instead of 404 page
Route::get('/locations/{user}', function (User $user) {
    return $user;
})->name('locations.view')
->missing(function (Request $request) {
    return Redirect::route('admin.dashboard');
});

## Defining Rate Limiters
// protected function configureRateLimiting()
// {
//     RateLimiter::for('global', function (Request $request) {
//         return Limit::perMinute(1000);
//     });
// }

// RateLimiter::for('custom_route', function () {
//     return Limit::perMinute(2);
// });
// ===== then
Route::get('custom_route', function () {
    return 'raza bangi';
})->middleware('throttle:custom_route');
// after this you can only 2 time hit the link per minute

## Accessing The Current Route
Route::get('show_route', function () {
    $route = Route::current(); // Illuminate\Routing\Route
    // dd($route); // show all routes
    $name = Route::currentRouteName(); // string
    // dd($name);
    $action = Route::getCurrentRequest(); // string
    // dd($action);
});


