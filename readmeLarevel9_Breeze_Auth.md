# Laravel Project Problem Solving Sequence 

-> View -> Routing -> Controller -> Model
Or,
-> Model & Migration -> View -> Routing -> Controller with db operation

# Laravel9_Breeze_Auth
# ---------------------------------------------------------------------------------

# Link
# ---------------------------------------------------------------------------------
https://www.udemy.com/course/laravel-advance-ecommerce-project/ ( Service Website Project with Blog )
 
# ---------------------------------------------------------------------------------

Section 3: Laravel 9 Breeze Authentication
# ---------------------------------------------------------------------------------
19. Laravel 9 Breeze Authentication install
     - composer require laravel/breeze --dev
     - php artisan breeze:install
     - npm install 
     - npm run dev
     - create database "Laravel9_Breeze_Auth"
     - php artisan migrate
     - run the app : "php artisan serve"  

20. Discuss on Laravel Breeze File Structure
     - See Auth folder on controller, view, route 
     - See all default route: "php artisan r:l"
     - Use of Auth middleware and 
     - Relation of auth middleware and its related route from routes(auth.php,web.php)
     ---Ex:---
     Route::middleware('auth')->group(function () { 
          Route::get('confirm-password', [ConfirmablePasswordController::class, 'show'])->name('password.confirm');
          Route::post('confirm-password', [ConfirmablePasswordController::class, 'store']);
          Route::post('logout', [AuthenticatedSessionController::class, 'destroy'])
                         ->name('logout');
     });
     ---------
 
21. Forgot Password & Password Reset
     - using mailtrip for fake smtp server (https://mailtrap.io)
          - configuring .env file after selecting framework in mailtrip 
          - "  MAIL_MAILER=smtp
               MAIL_HOST=smtp.mailtrap.io
               MAIL_PORT=2525
               MAIL_USERNAME=e7bb2f12328b01
               MAIL_PASSWORD=376bfdcdfc61a3
               MAIL_ENCRYPTION=tls
               MAIL_FROM_ADDRESS="support@laravel9_Service.com"
               MAIL_FROM_NAME="${APP_NAME}" 
            "
          - See mailtrip inbox and get the reset link 
     - Reset Password 

22. Email Verify in Laravel
     - Varify Email
          - see Middleware-> karnel.php -> 
          "'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,"
          - route -> web.php -> add a middleware on route "middleware(['auth','verified'])"
          - Model -> User.php -> "use Illuminate\Contracts\Auth\MustVerifyEmail;"
          - Model -> User.php -> add "implements MustVerifyEmail" with extends Authenticatable class
          - Then reg and login to acc. and face the email varification
          - Verify email from email(Here: from mailtrip fake server by getting mail)

     - Varify Email - extra functionality setting 
          - see Requests -> Auth -> LoginRequest.php 
               - wrong information login attenmpts
                    ----Ex----
                         public function ensureIsNotRateLimited()
                         {
                              // { // 4/5/ or more
                              if (! RateLimiter::tooManyAttempts($this->throttleKey(), 4)) {
                                   return;
                              }

                              event(new Lockout($this));

                              $seconds = RateLimiter::availableIn($this->throttleKey());

                              throw ValidationException::withMessages([
                                   'email' => trans('auth.throttle', [
                                        'seconds' => $seconds,
                                        'minutes' => ceil($seconds / 60),
                                   ]),
                              ]);
                         }
                    ----------

23. Changing our Logo Component
     - see views -> componets -> application-logo.blade.php 
          - Here, " <im/g src="{{ asset('//logo/RMIT.jpg')}}" width="100px"> </im/g> " // remove '/' from im/g

24. Logging in with Username
     - Edit Register
          - see migration and add "$table->string('username')->unique();" // no need to "php artisan migrate"
          - add database field "username" after 'password' field ( make 'username' unique )
          - use "php artisan r:l" to see all default routes and get the delails of routes
          - Find out the 'register.blade.php' and edit it  
          - Edit the controller and send data to model 
               "'email' => ['required', 'string', 'max:255', 'unique:users'],"
               &
               "$user = User::create([ 
                    'username' => $request->username,
                    ]);" 
          - Edit User.php  " 'username', "

     - Edit Login
          - See login.blade.php and edit for getting username field 
          - see Requests -> Auth -> LoginRequest.php
               - Edit and Change 'email' to 'username'
                    - In  rules() ->" 'username' => ['required', 'string'] ",
                    - In authenticate() -> 
                    "Auth::attempt($this->only('username', 'password')"
                    - In authenticate() -> 'username' => trans('auth.failed'),
                    - In ensureIsNotRateLimited() -> 'username' => trans('auth.throttle', [
                    - In  throttleKey() -> return Str::lower($this->input('username')).'|'.$this->ip();
