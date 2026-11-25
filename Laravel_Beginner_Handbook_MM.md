# Laravel Fundamentals Handbook

**အစပြုသူများအတွက် မြန်မာဘာသာ လက်တွေ့လမ်းညွှန်**  
Laravel 13 · PHP 8.3+ · Blade · SQLite

ဒီ handbook မှာ မှတ်စုတွေ ထည့်၊ ဖတ်၊ ပြင်၊ ဖျက်လို့ရတဲ့ **Notes App** တစ်ခု တည်ဆောက်မယ်။ အဆင့် ၁–၃ မှာ Laravel ရဲ့ အခြေခံအစိတ်အပိုင်းတွေကို သိလာမယ်။ အဆင့် ၄–၇ မှာ app ကို တည်ဆောက်ပြီး browser နဲ့ automated test နှစ်မျိုးလုံးကနေ စစ်မယ်။ Cheat sheets မှာ နေ့စဉ်ပြန်ကြည့်ဖို့ command နဲ့ syntax တွေ စုထားပါတယ်။

## ၁။ Laravel မစခင် သိထားသင့်တာ

- **PHP** မှာ variable (`$title`), array (`['title' => 'First note']`), function, class နဲ့ namespace ကို ဖတ်တတ်ရင် စလို့ရပါတယ်။
- **HTML form** ရဲ့ `name` က server ဆီပို့မယ့် field key ဖြစ်ပါတယ်။ ဥပမာ `name="title"` ကို Laravel မှာ `$request->input('title')` နဲ့ ဖတ်နိုင်ပါတယ်။
- **HTTP** မှာ `GET` နဲ့ page ဖတ်တယ်။ `POST` နဲ့ record အသစ်ထည့်တယ်။ `PATCH` နဲ့ record ပြင်တယ်။ `DELETE` နဲ့ record ဖျက်တယ်။
- **Composer** က PHP package တွေကို တပ်ဆင်ထိန်းသိမ်းပေးတယ်။ **Artisan** က Laravel project ထဲမှာ သုံးတဲ့ command-line tool ဖြစ်ပါတယ်။
- **Database** မှာ table က အမျိုးအစားတူ data တွေကို စုထားတယ်။ `notes` table ရဲ့ row တစ်ခုက note တစ်ခုဖြစ်ပြီး `id` က အဲဒီ row ကို ခွဲခြားပေးပါတယ်။

## ၂။ Project ဖွင့်ပြီး အရင်ဆုံး run ကြည့်မယ်

PHP 8.3 နှင့်အထက်၊ Composer နဲ့ PHP ရဲ့ SQLite PDO driver (`pdo_sqlite`) လိုပါတယ်။ ဒီလေ့ကျင့်ခန်းက Blade နဲ့ ရိုးရိုး HTML ကို သုံးထားလို့ Node.js setup ကို ကျော်လို့ရပါတယ်။

~~~bash
php -v
composer --version
php -m
composer create-project laravel/laravel notes-app "^13.0"
cd notes-app
php artisan --version
~~~

`php -m` အထွက်ထဲမှာ `pdo_sqlite` ပါရပါမယ်။ Project ရဲ့ `.env` မှာ ဒီ setting ရှိကြောင်း စစ်ပါ။

~~~dotenv
DB_CONNECTION=sqlite
~~~

`database/database.sqlite` ဖိုင် ရှိပြီးသားဆို ဆက်လုပ်နိုင်ပါတယ်။ ဖိုင်မရှိမှ project root ကနေ ဒီ command နဲ့ ဖန်တီးပါ။

~~~bash
php -r "touch('database/database.sqlite');"
~~~

~~~bash
php artisan migrate
php artisan serve
~~~

Browser မှာ **http://127.0.0.1:8000** ဖွင့်ပါ။ Laravel welcome page ပေါ်လာရင် setup အဆင်ပြေပါတယ်။ Server ကို run ထားပြီး နောက် Artisan commands တွေအတွက် project folder ထဲမှာ terminal နောက်တစ်ခု ဖွင့်ပါ။ ရပ်ချင်ရင် `Ctrl+C` နှိပ်ပါ။

[Laravel installation](https://laravel.com/docs/13.x/installation) · [PHP requirements](https://laravel.com/docs/13.x/deployment#server-requirements) · [SQLite configuration](https://laravel.com/docs/13.x/database#sqlite-configuration)

## ၃။ Request တစ်ခု Laravel ထဲမှာ ဘယ်လိုသွားလဲ

Browser က `GET /notes` တောင်းတဲ့အခါ **route** က ဘယ် handler ကို ခေါ်မလဲ ဆုံးဖြတ်တယ်။ **Middleware** က session နဲ့ request စစ်ဆေးမှုတွေကို ကိုင်တွယ်တယ်။ **Controller** က request အတွက် ဘာလုပ်ရမလဲ ဆုံးဖြတ်ပြီး **Model** က database ထဲက note တွေကို ယူပေးတယ်။ **Blade view** က ရလာတဲ့ data နဲ့ HTML ထုတ်ပေးတယ်။ Model, View နဲ့ Controller ကို အတိုကောက် **MVC** လို့ ခေါ်ပါတယ်။

| ဖိုင် / Folder | ဘာလုပ်လဲ |
| --- | --- |
| `routes/web.php` | Browser routes နဲ့ route names သတ်မှတ်တယ် |
| `app/Http/Controllers/` | Request လက်ခံပြီး response ပြန်ပေးတယ် |
| `app/Models/` | Eloquent model နဲ့ relationships သတ်မှတ်တယ် |
| `resources/views/` | `.blade.php` template တွေ ထားတယ် |
| `database/migrations/` | Table ဖန်တီးခြင်း၊ columns ပြောင်းခြင်းကို version ထိန်းတယ် |
| `tests/Feature/` | Route နဲ့ database ပါတဲ့ app behavior ကို စစ်တယ် |
| `.env` / `config/` | Environment values နဲ့ app configuration ကို ကိုင်တွယ်တယ် |

ပထမဆုံး route အနေနဲ့ `routes/web.php` ထဲက ရှိပြီးသား `use Illuminate\Support\Facades\Route;` ကို သုံးပြီး အောက်က code ထည့်ပါ။ Browser မှာ `/hello` ကို ဖွင့်ကြည့်ပါ။

~~~php
Route::get('/hello', function () {
    return 'Hello, Laravel!';
})->name('hello');
~~~

`hello` က route name ဖြစ်ပါတယ်။ `route('hello')` နဲ့ URL ပြန်ထုတ်နိုင်လို့ path ပြောင်းရင် link တိုင်းကို ပြင်စရာမလိုပါဘူး။

[Routing](https://laravel.com/docs/13.x/routing) · [Request lifecycle](https://laravel.com/docs/13.x/lifecycle)

## ၄။ Model နဲ့ Migration ဆောက်မယ်

~~~bash
php artisan make:model Note -m
php artisan make:controller NoteController
~~~

`-m` က model နဲ့အတူ migration ဖိုင်ပါ ထုတ်ပေးတယ်။ `database/migrations/` ထဲမှာ အခုထွက်လာတဲ့ `…_create_notes_table.php` ကို အောက်ကအတိုင်း ပြင်ပါ။

~~~php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('notes', function (Blueprint $table) {
            $table->id();
            $table->string('title', 120);
            $table->text('body')->nullable();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('notes');
    }
};
~~~

`up()` က schema ပြောင်းလဲမှုကို apply လုပ်ပြီး `down()` က ပြန်ဖျက်ပေးတယ်။ `timestamps()` က `created_at` နဲ့ `updated_at` ထည့်ပေးပါတယ်။ `body` ကို optional ထားချင်လို့ `nullable()` သုံးထားတယ်။

`app/Models/Note.php` ကို ဒီလို ပြင်ပါ။

~~~php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Note extends Model
{
    protected $fillable = ['title', 'body'];
}
~~~

Eloquent က `Note` model ကို `notes` table နဲ့ convention အရ ချိတ်ပေးတယ်။ `$fillable` က `create()` နဲ့ `update()` လို method တွေမှာ array ကနေ ဖြည့်ခွင့်ရှိတဲ့ attributes ကို သတ်မှတ်တယ်။ Request က data ကို စစ်ဖို့ validation လည်း သီးခြားသုံးမယ်။

~~~bash
php artisan migrate
php artisan migrate:status
~~~

[Database migrations](https://laravel.com/docs/13.x/migrations) · [Eloquent models](https://laravel.com/docs/13.x/eloquent)

## ၅။ Routes နဲ့ Controller ချိတ်မယ်

`routes/web.php` ရဲ့ import တွေနားမှာ `use App\Http\Controllers\NoteController;` ထည့်ပါ။ ပြီးရင် ဒီ route ကြေညာချက် ထည့်ပါ။

~~~php
Route::resource('notes', NoteController::class)
    ->only(['index', 'store', 'edit', 'update', 'destroy']);
~~~

| HTTP method | URL | Controller method | Route name |
| --- | --- | --- | --- |
| GET | `/notes` | `index` | `notes.index` |
| POST | `/notes` | `store` | `notes.store` |
| GET | `/notes/{note}/edit` | `edit` | `notes.edit` |
| PUT / PATCH | `/notes/{note}` | `update` | `notes.update` |
| DELETE | `/notes/{note}` | `destroy` | `notes.destroy` |

`app/Http/Controllers/NoteController.php` ကို ဒီ code နဲ့ အစားထိုးပါ။

~~~php
<?php

namespace App\Http\Controllers;

use App\Models\Note;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\View\View;

class NoteController extends Controller
{
    public function index(): View
    {
        return view('notes.index', [
            'notes' => Note::orderByDesc('id')->paginate(10),
        ]);
    }

    public function store(Request $request): RedirectResponse
    {
        Note::create($this->validatedNote($request));

        return redirect()->route('notes.index')->with('status', 'Note saved.');
    }

    public function edit(Note $note): View
    {
        return view('notes.edit', ['note' => $note]);
    }

    public function update(Request $request, Note $note): RedirectResponse
    {
        $note->update($this->validatedNote($request));

        return redirect()->route('notes.index')->with('status', 'Note updated.');
    }

    public function destroy(Note $note): RedirectResponse
    {
        $note->delete();

        return redirect()->route('notes.index')->with('status', 'Note deleted.');
    }

    private function validatedNote(Request $request): array
    {
        return $request->validate([
            'title' => ['required', 'string', 'max:120'],
            'body' => ['nullable', 'string', 'max:5000'],
        ]);
    }
}
~~~

- **Route model binding** က `{note}` နဲ့ `Note $note` ကို တိုက်ပြီး URL ထဲက ID နဲ့ record ရှာပေးတယ်။ ဥပမာ `/notes/7/edit` က ID 7 ကို ရှာတယ်။ မတွေ့ရင် Laravel က 404 ပြန်ပေးတယ်။
- **Validation** အောင်မြင်ရင် စစ်ထားတဲ့ field တွေပဲ array အနေနဲ့ ရပါတယ်။ ပုံမှန် browser form မှာ မအောင်မြင်ရင် Laravel က အရင် page ကို ပြန်ပို့ပြီး errors နဲ့ old input ကို session ထဲ ယာယီထည့်ပေးတယ်။
- **Redirect** က data သိမ်းပြီးနောက် browser ကို list page ပြန်ပို့တယ်။ `with('status', ...)` က နောက် request မှာ ပြဖို့ status စာသား ပေးလိုက်တာပါ။

~~~bash
php artisan route:list --path=notes
~~~

[Resource controllers](https://laravel.com/docs/13.x/controllers#resource-controllers) · [Route model binding](https://laravel.com/docs/13.x/routing#route-model-binding) · [Validation](https://laravel.com/docs/13.x/validation)

## ၆။ Blade Views ဆောက်မယ်

`resources/views/notes/` folder ဖန်တီးပြီး အောက်က ဖိုင်လေးခုကို ထည့်ပါ။

**`resources/views/notes/layout.blade.php`**

~~~blade
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Notes App</title>
</head>
<body>
    <main>
        @yield('content')
    </main>
</body>
</html>
~~~

**`resources/views/notes/fields.blade.php`**

~~~blade
<label for="title">Title</label>
<input id="title" name="title" value="{{ old('title', $note->title ?? '') }}" maxlength="120">
@error('title')
    <p role="alert">{{ $message }}</p>
@enderror

<label for="body">Body</label>
<textarea id="body" name="body" maxlength="5000">{{ old('body', $note->body ?? '') }}</textarea>
@error('body')
    <p role="alert">{{ $message }}</p>
@enderror
~~~

**`resources/views/notes/index.blade.php`**

~~~blade
@extends('notes.layout')

@section('content')
    <h1>Notes</h1>

    @if (session('status'))
        <p role="status">{{ session('status') }}</p>
    @endif

    <form method="POST" action="{{ route('notes.store') }}">
        @csrf
        @include('notes.fields', ['note' => null])
        <button type="submit">Add note</button>
    </form>

    @forelse ($notes as $note)
        <article>
            <h2>{{ $note->title }}</h2>
            <p>{{ $note->body }}</p>
            <a href="{{ route('notes.edit', $note) }}">Edit</a>

            <form method="POST" action="{{ route('notes.destroy', $note) }}">
                @csrf
                @method('DELETE')
                <button type="submit">Delete</button>
            </form>
        </article>
    @empty
        <p>No notes yet.</p>
    @endforelse

    @if ($notes->hasPages())
        <nav aria-label="Notes pages">
            @if ($notes->previousPageUrl())
                <a href="{{ $notes->previousPageUrl() }}">Previous</a>
            @endif
            <span>Page {{ $notes->currentPage() }} of {{ $notes->lastPage() }}</span>
            @if ($notes->nextPageUrl())
                <a href="{{ $notes->nextPageUrl() }}">Next</a>
            @endif
        </nav>
    @endif
@endsection
~~~

**`resources/views/notes/edit.blade.php`**

~~~blade
@extends('notes.layout')

@section('content')
    <h1>Edit note</h1>

    <form method="POST" action="{{ route('notes.update', $note) }}">
        @csrf
        @method('PATCH')
        @include('notes.fields', ['note' => $note])
        <button type="submit">Save changes</button>
    </form>

    <a href="{{ route('notes.index') }}">Back to notes</a>
@endsection
~~~

`@extends` နဲ့ `@section` က layout ပြန်သုံးပေးပြီး `@include` က form fields တွေကို page နှစ်ခုမှာ သုံးပေးတယ်။ `{{ ... }}` က HTML special characters တွေကို escape လုပ်ပေးလို့ note ထဲက HTML ကို စာသားအဖြစ် ပြပေးပါတယ်။ `@csrf` က form token ထည့်ပေးတယ်။ HTML form မှာ `POST` သုံးပြီး `@method('PATCH')` သို့မဟုတ် `@method('DELETE')` ထည့်ပေးရင် Laravel က သက်ဆိုင်ရာ route နဲ့ ချိတ်ပေးတယ်။

[Blade templates](https://laravel.com/docs/13.x/blade) · [Pagination](https://laravel.com/docs/13.x/pagination)

## ၇။ Browser နဲ့ Feature Test မှာ စစ်မယ်

**http://127.0.0.1:8000/notes** ကို ဖွင့်ပြီး အောက်ကအတိုင်း စမ်းပါ။

1. Note မရှိသေးရင် **No notes yet.** ပေါ်မယ်။
2. Title မထည့်ဘဲ **Add note** နှိပ်ရင် title error ပေါ်မယ်။ Body ထည့်ထားရင် ဆက်ရှိနေမယ်။
3. Title ထည့်ပြီး သိမ်းရင် note ကို list မှာ မြင်ရမယ်။
4. **Edit** နဲ့ title ပြင်ပြီး သိမ်းရင် ပြင်ထားတဲ့စာ ပေါ်မယ်။
5. **Delete** နှိပ်လိုက်ရင် note ကို list မှာ မတွေ့တော့ဘူး။
6. Note ၁၁ ခု ထည့်ကြည့်ရင် pagination ပေါ်မယ်။

Automated test အတွက် `phpunit.xml` ထဲမှာ testing DB ကို SQLite in-memory သုံးထားကြောင်း စစ်ပါ။ ရှိပြီးသား entries တွေကို လိုအပ်ရင် ပြင်ပါ။

~~~xml
<env name="DB_CONNECTION" value="sqlite"/>
<env name="DB_DATABASE" value=":memory:"/>
~~~

`tests/Feature/NoteTest.php` ဖန်တီးပါ။

~~~php
<?php

namespace Tests\Feature;

use App\Models\Note;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class NoteTest extends TestCase
{
    use RefreshDatabase;

    public function test_a_note_can_be_created_edited_and_deleted(): void
    {
        $this->post(route('notes.store'), [
            'title' => 'Learn Blade',
            'body' => 'Practice form validation.',
        ])->assertRedirect(route('notes.index'));

        $note = Note::where('title', 'Learn Blade')->firstOrFail();

        $this->get(route('notes.index'))->assertOk()->assertSee('Learn Blade');
        $this->get(route('notes.edit', $note))->assertOk()->assertSee('Learn Blade');

        $this->patch(route('notes.update', $note), [
            'title' => 'Practice Blade',
            'body' => null,
        ])->assertRedirect(route('notes.index'));

        $this->assertDatabaseHas('notes', [
            'id' => $note->id,
            'title' => 'Practice Blade',
        ]);

        $this->delete(route('notes.destroy', $note))
            ->assertRedirect(route('notes.index'));

        $this->assertDatabaseMissing('notes', ['id' => $note->id]);
    }

    public function test_title_is_required(): void
    {
        $this->from(route('notes.index'))
            ->post(route('notes.store'), ['title' => ''])
            ->assertRedirect(route('notes.index'))
            ->assertSessionHasErrors('title');

        $this->assertDatabaseCount('notes', 0);
    }
}
~~~

~~~bash
php artisan config:clear
php artisan test --filter=NoteTest
~~~

အောင်မြင်ရင် test နှစ်ခု pass ဖြစ်ပါမယ်။ `RefreshDatabase` က test တွေကို သန့်ရှင်းတဲ့ database state ကနေ စတင်နိုင်အောင် ကူညီပါတယ်။

[HTTP tests](https://laravel.com/docs/13.x/http-tests) · [Database testing](https://laravel.com/docs/13.x/database-testing)

## ၈။ နောက်တစ်ဆင့် လေ့ကျင့်ခန်း

| အဆင့် | လုပ်ကြည့်ရန် | ပြီးပြီလို့ စစ်နိုင်မယ့်အချက် |
| --- | --- | --- |
| ၁ | Note title ရှာဖို့ `GET` form နဲ့ query parameter `q` ထည့်ပါ | ရှာထားတဲ့စာသားနဲ့ ကိုက်တဲ့ title တွေပဲ ပေါ်ပြီး page ပြောင်းလည်း filter ဆက်ရှိမယ် |
| ၂ | `is_pinned` column ကို migration အသစ်နဲ့ ထည့်ပါ | ရှိပြီးသား notes တွေ ဆက်ပေါ်ပြီး pinned notes တွေ အပေါ်ဆုံးရောက်မယ် |
| ၃ | Category ထည့်ပြီး `Note` မှာ `belongsTo`၊ `Category` မှာ `hasMany` သတ်မှတ်ပါ | List မှာ note တစ်ခုစီရဲ့ category name ပေါ်မယ်။ `with('category')` နဲ့ ကြိုယူပါ |
| ၄ | Login နဲ့ note ownership ထည့်ပါ | User တစ်ယောက်က သူ့ note တွေပဲ မြင်၊ ပြင်၊ ဖျက်နိုင်မယ်။ Policy နဲ့ feature tests သုံးပါ |

ဒီ tutorial ရဲ့ Notes App က login မပါတဲ့ local practice app ဖြစ်ပါတယ်။ Account ပါတဲ့ app မှာ **authentication** က request ပို့သူ ဘယ်သူလဲ စစ်ပြီး **authorization** က အဲဒီသူ ဘာလုပ်ခွင့်ရှိလဲ စစ်ပေးပါတယ်။

[Eloquent relationships](https://laravel.com/docs/13.x/eloquent-relationships) · [Authentication](https://laravel.com/docs/13.x/authentication) · [Authorization](https://laravel.com/docs/13.x/authorization)
