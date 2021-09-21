## Laravel tinker

Trong quá trình code cũng như debug chúng ta thường thêm helper dd() hay var_dump() die() vào trong code, sau đó refresh lại trang hoặc postman (công cụ call api), và rồi quan sát kết quả, lặp lại quá trình này rất nhiều lần, nó làm chúng ta mất thời gian hơn cho việc debug. Thay vì những cách debug này chúng ta có thể dùng tinker.
Tinker là một tool có dạng REPL (read-eval-print loop), tức là một tool cho phép lặp đi lặp lại việc đọc dữ liệu, tính toán dữ liệu và in ra màn hình.

Chúng ta sử dụng tinker thông qua command của laravel `php artisan tinker`.

Tinker cho phép bạn tương tác với toàn bộ ứng dụng Laravel của mình với command line, như tương tác với Eloquent ORM, Collection, jobs, events, những function của PHP....

- Tương tác Eloquent ORM: Thay đổi thông tin của user. 

```
$updateUser = App\Models\MUser::first();
$updateUser->phone_verified = true;
$updateUser->save();
```

- Tương tác Eloquent ORM: Tạo một user mới

```
$user = new App\Models\MUser();
$user->hash_id = Str::random(8);
$user->auth0_sub = Str::random(25);
$user->phone_number = '0123456788';
$user->is_random_email = false;
$user->orgs_code = 'A001';
$user->email = 'test1@gmail.com';
$user->phone_verified = false;
$user->save();
```

- Làm việc với Collection:

```
$collection = collect([
    ['name' => 'Test1', 'value' => 'Value1'],
    ['name' => 'Test2', 'value' => 'Value2'],
    ['name' => 'Test3', 'value' => 'Value3'],
    ['name' => 'Test4', 'value' => 'Value4']
]);
$collection->pluck('name');
```

- Call function trong Repository

```
$userRepo = App(App\Interfaces\UserRepositoryInterface::class);
$userRepo->findByAttr(['auth0_sub' => 'auth0|1000']);
```

- Call function trong Service

```
$service = app(App\Services\AuthService::class);
$input = ['login_id' => 'quynhnt-tda024', 'password' => 'Uw4AEo6x/9Y=', 'company_id' => 10012];
$service->loginAuth0($input);
```

- Debug kết quả của helper:

```
openssl_decrypt('Uw4AEo6x/9Y=', 'AES-128-CTR', env('DECRYPTION_KEY'), OPENSSL_ZERO_PADDING, env('DECRYPTION_IV'));
```

- Kiểm tra tính đúng đắn của những đoạn script code chuẩn bị dùng cho một logic nào đó.

```
$users = [];

for ($i = 0; $i <=1000; $i++) {
    $user = [
        'email' => "test$i@test.io",
        'user_id' => "test$i",
        'username' => "test$i",
        'custom_password_hash' => [
            'algorithm' => 'md5',
            'hash' => [
                'value' => 'MjE0YWFmMmM5YTg1MTBkOTQ4NTU1ZWUyNWNiMzgzOTc=',
                'encoding' => 'base64',
            ],
        ],
    ];
    array_push($users, $user);
}

Storage::disk('local')->put('import.json', json_encode($users, JSON_PRETTY_PRINT));
```
## Tổng kết.
- Ưu điểm: Tinker là 1 tool debug mạnh mẽ, xứng đáng được recommend cho những người phát triển.
- Nhược điểm: Nhược điểm duy nhất là nếu bạn thay đổi code trong ứng dụng thì phải open lại tinker để check đoạn code mới này.
