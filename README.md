# Stripe-Payment-Intergration
Laravel 10 Stripe Payment Gateway 

1st step 
sub sa pehla apna stripe devloper mode sa stripe STRIPE_KEY , STRIPE_SECRET ye dono lani ha fir is link sa api mil jaye gy i mean bioth key 
https://dashboard.stripe.com/test/apikeys
 
2nd step set in env file 
STRIPE_KEY=
STRIPE_SECRET=

3rd step 
donload stripe package  this command run in terminal
composer require stripe/stripe-php

4th make controller fior stripe chosse controlelr name 
StripePaymentController

5th step 
make thereee route  1st route for showing package and seconf for your polan send to stripe and  third for succcess route 
Route::get('/stripe', [StripePaymentController::class, 'stripe'])->name('stripe.index');
Route::match(['get', 'post'], '/stripe/checkout', [StripePaymentController::class, 'stripeCheckout'])->name('stripe.checkout');
Route::match(['get', 'post'],'/stripe/checkout/success', [StripePaymentController::class, 'stripeCheckoutSuccess'])->name('stripe.checkout.success');

6th controller code 

<?php

namespace App\Http\Controllers;

use Stripe;
use Illuminate\View\View;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Session;

class StripePaymentController extends Controller
{
    public function stripe() : View 
    {
        // dd('here');
        return view('stripe');
    }

    public function stripeCheckout(Request $request) 
{

    $stripe =  new \Stripe\StripeClient(env('STRIPE_SECRET'));
    $redirectUrl = route('stripe.checkout.success') . "?session_id={CHECKOUT_SESSION_ID}";
    $response = $stripe->checkout->sessions->create([                    

        'success_url' => $redirectUrl,

        'customer_email' => 'talharao997az@gmail.com',

        'payment_method_types' => ['link', 'card'],

        'line_items' => [[
          'price_data' => [
            'product_data' => [
              'name' => $request->product,
            ],
            'unit_amount' => $request->price * 100,
            'currency' => 'usd',
        ],
        'quantity' => 1,
    ],
    ],
    'mode' => 'payment',
    'allow_promotion_codes' => true,
    ]);
    // dd($response->id);
    Session::put('stripe_id',$response->id);
    return redirect($response->url);
}


public function stripeCheckoutSuccess(Request $request) 
{
  ye code agar client ky detail fetch krni hai tou watrna comment krden // $stripe_checkout_id = Session::get('stripe_id');


    $stripe =  new \Stripe\StripeClient(env('STRIPE_SECRET'));
    ye line bhi client ky detail shwo krna k liye hai agaar apan apana pass record akhin show krwana hou to ye line or upar wali un comment kr k data fetch kr sakat hen dd sa check k r k // $checkoutSession = $stripe->checkout->sessions->retrieve($stripe_checkout_id, []);
  //dd($checkoutSession);  
    return redirect()->route('stripe.index')->with(['success' => 'Payment successful!']);

}
}

and this is my balde code  shwoing plan



<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>STRIPE LARAVEL</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.2/dist/css/bootstrap.min.css"
        integrity="sha384-xOolHFLEh07PJGoPkLv1IbcEPTNtaed2xpHsD9ESMhqIYd0nLMwNLD69Npy4HI+N" crossorigin="anonymous">
</head>

<body>

    <div class="container">
        <h1 class="text-center mb-5 mt-5">
            stripe checkout example
        </h1>
        @if (Session::has('success'))
            <div class="alert alert-success">
                {{ Session::get('success') }}

                @php
                    Session::forget('success');
                @endphp
            </div>
        @endif
        <div class="row">
            <div class="col-md-4">
                <div class="card" style="18rem;">
                    <img src="https://picsum.photos/200/300" class="card-img-top">
                    <div class="card-body">
                        <h5 class="card-title"> Silver</h5>
                        <p class="card-text">
                            Lorem ipsum dolor sit, amet consectetur adipisicing elit. Dolorem minima ut vero iure
                            impedit magni hic distinctio! 
                        </p>
                        <a href="{{ route('stripe.checkout', ['price' => 10, 'product' => 'silver']) }}" class="btn btn-primary">
                        Pay Now
                        </a>
                    </div>
                </div>
            </div>

            <div class="col-md-4">
                <div class="card" style="18rem;">
                    <img src="https://picsum.photos/200/300" class="card-img-top">
                    <div class="card-body">
                        <h5 class="card-title"> Gold</h5>
                        <p class="card-text">
                            Lorem ipsum dolor sit, amet consectetur adipisicing elit. Dolorem minima ut vero iure
                            impedit magni hic distinctio! Nam consectetur, 
                        </p>
                        <a href="{{ route('stripe.checkout', ['price' => 100, 'product' => 'gold']) }}" class="btn btn-primary">
                        Pay Now
                        </a>
                    </div>
                </div>
            </div>



            <div class="col-md-4">
                <div class="card" style="18rem;">
                    <img src="https://picsum.photos/200/300" class="card-img-top">
                    <div class="card-body">
                        <h5 class="card-title"> Platinium</h5>
                        <p class="card-text">
                            Lorem ipsum dolor sit, amet consectetur adipisicing elit. Dolorem minima ut vero iure
                        
                        </p>
                        <a href="{{ route('stripe.checkout', ['price' => 1000, 'product' => 'platinum']) }}" class="btn btn-primary">
                        Pay Now
                        </a>
                    </div>
                </div>
            </div>


        </div>
    </div>
</body>

</html>



