# Laravel Fundamentals Cheat Sheets

**မြန်မာဘာသာ အမြန်ကိုးကားရန်** · Laravel 13 · PHP 8.3+  
ဥပမာတွေမှာ `Note` model၊ `notes` table နဲ့ handbook ထဲက `notes.*` routes ကို သုံးထားပါတယ်။ PHP snippet တွေကို သက်ဆိုင်ရာ route၊ controller၊ model သို့မဟုတ် Tinker မှာ သုံးပါ။

## ၁။ Artisan နှင့် Composer

| လုပ်ချင်တာ | Command |
| --- | --- |
| Project အသစ် | `composer create-project laravel/laravel notes-app "^13.0"` |
| ရှိပြီးသား project ရဲ့ dependencies တပ်ဆင် | `composer install` |
| Laravel version စစ် | `php artisan --version` |
| Local development server | `php artisan serve` |
| Notes routes ရှာ | `php artisan route:list --path=notes` |
| Model + migration | `php artisan make:model Note -m` |
| Resource controller scaffold | `php artisan make:controller NoteController --resource --model=Note` |
| Form Request scaffold | `php artisan make:request StoreNoteRequest` |
| Migration အသစ် | `php artisan make:migration add_is_pinned_to_notes_table --table=notes` |
| မလုပ်ရသေးတဲ့ migrations run | `php artisan migrate` |
| Migration status ကြည့် | `php artisan migrate:status` |
| လတ်တလော migration batch ကို ပြန်ဆုတ် | `php artisan migrate:rollback` |
| Seeder run | `php artisan db:seed` |
| PHP shell ထဲကနေ app စမ်း | `php artisan tinker` |
| Test အကုန် / တစ်ခုချင်း စစ် | `php artisan test` / `php artisan test --filter=NoteTest` |
| Cached configuration ရှင်း | `php artisan config:clear` |

`migrate:rollback` က migration ရဲ့ `down()` ကို run တယ်။ `migrate:fresh` က သတ်မှတ်ထားတဲ့ database connection ရဲ့ tables အားလုံးကို ဖျက်ပြီး migrations ပြန် run တယ်။ ဖျက်လို့ရတဲ့ local database မှာသာ `migrate:fresh` သုံးပါ။

[Artisan](https://laravel.com/docs/13.x/artisan) · [Migrations](https://laravel.com/docs/13.x/migrations)

## ၂။ Route၊ Request နှင့် Response

~~~php
// routes/web.php
use App\Http\Controllers\NoteController;
use Illuminate\Support\Facades\Route;

Route::get('/notes', [NoteController::class, 'index'])->name('notes.index');
Route::get('/notes/{note}/edit', [NoteController::class, 'edit'])->name('notes.edit');
~~~

Resource route ရှိပြီးသား project မှာ အထက်က manual routes တွေက အစားထိုးရေးနည်း ဖြစ်ပါတယ်။

| ရယူချင်တာ | Syntax |
| --- | --- |
| Named route URL | `route('notes.index')` |
| Model parameter ပါတဲ့ URL | `route('notes.edit', $note)` |
| Query string ဖတ် | `$request->query('q', '')` |
| Request input ဖတ် | `$request->input('title')` |
| Route က ID ကို Model အဖြစ်ရ | `public function edit(Note $note)` |
| Blade view နဲ့ data ပြန် | `return view('notes.edit', ['note' => $note]);` |
| Redirect + flash message | `return redirect()->route('notes.index')->with('status', 'Saved');` |
| JSON ပြန် | `return response()->json(['id' => $note->id], 201);` |

[Routing](https://laravel.com/docs/13.x/routing) · [Requests](https://laravel.com/docs/13.x/requests) · [Responses](https://laravel.com/docs/13.x/responses)

## ၃။ Blade နှင့် Form Validation

| လုပ်ချင်တာ | Syntax |
| --- | --- |
| HTML escape လုပ်ပြီး ပြ | `{{ $note->title }}` |
| Condition စစ် | `@if (...) … @endif` |
| List / empty state ပြ | `@forelse ($notes as $note) … @empty … @endforelse` |
| Layout သုံး / content သတ်မှတ် | `@extends('notes.layout')` / `@section('content') … @endsection` |
| Layout ထဲမှာ content ပြ | `@yield('content')` |
| View တစ်ပိုင်း ထည့် | `@include('notes.fields', ['note' => $note])` |
| Form ထဲ CSRF token ထည့် | `@csrf` |
| POST form ကနေ update / delete route ခေါ် | `@method('PATCH')` / `@method('DELETE')` |
| Validation fail ပြီး အရင်တန်ဖိုး ပြန်ပြ | `{{ old('title', $note->title ?? '') }}` |
| Field error ပြ | `@error('title') {{ $message }} @enderror` |
| Session message ပြ | `{{ session('status') }}` |

~~~php
$data = $request->validate([
    'title' => ['required', 'string', 'max:120'],
    'body' => ['nullable', 'string', 'max:5000'],
]);

Note::create($data);
~~~

- `required` က တန်ဖိုးတောင်းတယ်။ `nullable` က `null` ကို လက်ခံတယ်။ `string` နဲ့ `max:120` တွဲသုံးရင် စာသားအရှည် ၁၂၀ အထိ လက်ခံတယ်။
- Form မအောင်မြင်ရင် browser flow မှာ redirect နဲ့ session errors ရတယ်။ JSON response တောင်းထားတဲ့ request မှာ validation error က HTTP **422** ဖြစ်တယ်။
- Validation logic ကြီးလာရင် **Form Request** ထဲက `rules()` ကို သုံးနိုင်တယ်။ `authorize()` မှာ request လုပ်ခွင့် သတ်မှတ်ပြီး controller ထဲမှာ `$request->validated()` နဲ့ data ယူပါ။

[Blade](https://laravel.com/docs/13.x/blade) · [Validation](https://laravel.com/docs/13.x/validation)

## ၄။ Eloquent နှင့် Database

| ရည်ရွယ်ချက် | Syntax |
| --- | --- |
| ID နဲ့ရှာ၊ မတွေ့ရင် 404 ပြန်စေ | `Note::findOrFail($id)` |
| သတ်မှတ်ချက်နဲ့ ရှာ | `Note::where('title', $title)->get()` |
| ပထမဆုံး record ရယူ | `Note::where('title', $title)->first()` |
| Record ရှိမရှိ စစ် | `Note::where('title', $title)->exists()` |
| တစ်မျက်နှာ ၁၀ ခု ပြ | `Note::orderByDesc('id')->paginate(10)` |
| Pagination URL ထဲ filter ဆက်ထည့် | `Note::orderByDesc('id')->paginate(10)->withQueryString()` |
| အသစ်ထည့် | `Note::create($data)` |
| ပြင် / ဖျက် | `$note->update($data)` / `$note->delete()` |
| Table columns သတ်မှတ် | `$table->string('title', 120)` / `$table->text('body')->nullable()` |
| Existing table ပြောင်း | Migration အသစ်ထဲမှာ `Schema::table('notes', ...)` |
| Data နမူနာ ထုတ် / ထည့် | **Factory** နဲ့ records ပုံစံသတ်မှတ်၊ **Seeder** နဲ့ database ထဲ ထည့် |

`first()` နဲ့ `find()` က record မတွေ့ရင် `null` ပြန်ပေးတယ်။ `create($data)` နဲ့ `update($data)` အတွက် model ရဲ့ `$fillable` ထဲမှာ သက်ဆိုင်ရာ fields ပါရမယ်။ `withQueryString()` က လက်ရှိ filter ကို pagination link တွေထဲ ထည့်ပေးတယ်။ Filter ကို query မှာ `where(...)` နဲ့ အရင် apply လုပ်ပေးရပါတယ်။

**One-to-many** နမူနာမှာ `notes.category_id` က `categories.id` ကို ညွှန်းတယ်။

| နေရာ | Relation / Query |
| --- | --- |
| `Category` model ရဲ့ `notes()` | `return $this->hasMany(Note::class);` |
| `Note` model ရဲ့ `category()` | `return $this->belongsTo(Category::class);` |
| Notes နဲ့ categories အတူယူ | `Note::with('category')->orderByDesc('id')->get()` |

`with('category')` က categories ကို ကြိုယူပေးလို့ note တစ်ခုချင်းစီအတွက် ထပ် query လုပ်ရတာကို လျှော့ပေးတယ်။

[Eloquent](https://laravel.com/docs/13.x/eloquent) · [Relationships](https://laravel.com/docs/13.x/eloquent-relationships) · [Pagination](https://laravel.com/docs/13.x/pagination) · [Seeding](https://laravel.com/docs/13.x/seeding)

## ၅။ Tests နှင့် Troubleshooting

~~~php
$this->get(route('notes.index'))->assertOk();

$this->from(route('notes.index'))
    ->post(route('notes.store'), ['title' => ''])
    ->assertRedirect(route('notes.index'))
    ->assertSessionHasErrors('title');

$this->assertDatabaseHas('notes', ['title' => 'Learn Blade']);
~~~

`assertDatabaseHas()` ကို အဲဒီ record ဖန်တီးပြီးမှ ခေါ်ပါ။ Database test အတွက် testing connection ကို SQLite `:memory:` ထားပြီး `RefreshDatabase` သုံးနိုင်ပါတယ်။

| တွေ့ရတာ | အရင်စစ်ရမယ့်နေရာ |
| --- | --- |
| `Could not open input file: artisan` | `artisan` ဖိုင်ရှိတဲ့ project root ထဲဝင်ပါ |
| `could not find driver` | `php -m` ထဲ `pdo_sqlite` / `pdo_mysql` ပါသလား စစ်ပါ |
| `No application encryption key` | Local project မှာ `.env` ရှိပြီး `APP_KEY` လွတ်နေရင် `php artisan key:generate` run ပါ |
| `no such table: notes` | `.env` DB connection နဲ့ `php artisan migrate:status` စစ်ပါ |
| 404 | URL ကို route list နဲ့ တိုက်ပါ။ Model binding ပါရင် ID ရှိကြောင်း စစ်ပါ |
| 405 Method Not Allowed | Form ရဲ့ HTTP method နဲ့ route list တိုက်ပါ။ Update/Delete အတွက် `@method(...)` စစ်ပါ |
| 419 Page Expired | Form မှာ `@csrf` ပါသလား၊ session သက်တမ်းကုန်သလား စစ်ပါ |
| `View [notes.index] not found` | `resources/views/notes/index.blade.php` ရှိကြောင်း စစ်ပါ |
| `.env` ပြင်ပြီး setting အဟောင်းပဲ သုံးနေ | `php artisan config:clear` run ပါ |
| 500 | Local မှာ `storage/logs/` ထဲက log ကို ဖတ်ပြီး stack trace ကို စစ်ပါ |

`.env` ထဲက credentials ကို Git ထဲ မတင်ပါနဲ့။ Production configuration မှာ `APP_DEBUG=false` ထားပါ။

[HTTP tests](https://laravel.com/docs/13.x/http-tests) · [Configuration](https://laravel.com/docs/13.x/configuration) · [CSRF protection](https://laravel.com/docs/13.x/csrf)
