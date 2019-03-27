# paginate

- [Setup](#setup)
  - [Composer](#composer)
  - [Publish configuration](#Publish-configuration)
- [Sử dụng](#Sử-dụng)
  - [Model](#Model)
  - [Controller](#Controller)
  - [view blade](#Blade-Extension)
## setup

### composer
+ thêm đoạn code vào `` require `` trong file `` composer.json `` :
   `` "require": {
        "kyslik/column-sortable": "5.8.*"
      } ``
+ chạy `` composer install `` 
### Laravel >= 5.5: Laravel tự động làm.
### Laravel < 5.5: thêm code vào ``service provide`` trong `` config/app ``:

```
  'providers' => [

      App\Providers\RouteServiceProvider::class,

      /*
       * Third Party Service Providers...
       */
      Kyslik\ColumnSortable\ColumnSortableServiceProvider::class,
  ],
```

### Publish configuration

Chạy lệnh:

+ `` php artisan vendor:publish --provider="Kyslik\ColumnSortable\ColumnSortableServiceProvider" --tag="config" ``

=> cài đặt, tùy chỉnh nằm file trong: `` config/columnsortable ``

## sử dụng

### Model:
````
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Kyslik\ColumnSortable\Sortable;

class Category extends Model
{
    use Sortable;
    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name'
    ];

    public $sortable = [
        'id',
        'name',
    ];
}
````

thêm:  
+  use Kyslik\ColumnSortable\Sortable;
+   use Sortable;
 +  sortable[' ']; : chứa các column trong DB cần sort

### Controller :
```` 
<?php

namespace App\Http\Controllers;

use App\Models\Category;

class PostsController extends Controller
{
    /**
     * Create a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
    }

    /**
     * Show the application dashboard.
     *
     * @return \Illuminate\Contracts\Support\Renderable
     */
    public function index(Category $category)
    {
        $categories = $category->sortable()->paginate(10);

        return view('posts', compact('categories'));
    }
}
````

sử dụng:   

```` 
$categories = $category->sortable()->paginate(10);

// page is loaded for first time be applied when URL is empty
$categories = $category->sortable(['name' => 'desc'])->paginate(10); 
 ````
### Blade Extension

Thêm 2 dòng:

````
@sortablelink('column', 'Title', ['parameter' => 'smile'],  ['class' => ''])

{!! $users->appends(\Request::except('page'))->render() !!}
````




Trong đó : 
+ **@sortablelink()**: thẻ < a/> để sort
	+ tham số 1: tên cột cần sort (cột được khai báo ở sortable[] trong model).
  + tham số 2: Tên hiển thị ra
  + tham số 3: danh sách các query strings parameter ( default GET)
  + tham số 4: thêm các thông số cho thẻ.
  + Option: 
      ````
      @sortablelink('name')
      @sortablelink('name', 'Username')
      @sortablelink('address', trans('fields.address'), ['filter' => 'active, visible'])
      @sortablelink('address', trans('fields.address'), ['filter' => 'active, visible'], ['class' => 'btn btn-block', 'rel' => 'nofollow'])
    ````
+ **{!! $categories->appends(\Request::except('page'))->render() !!}** : các page
![](https://i.ibb.co/58HjLs3/Capture.png)

