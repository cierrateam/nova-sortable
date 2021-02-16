# Nova Sortable

This package is forked from [optimistdigital/nova-sortable](https://github.com/optimistdigital/nova-sortable). The difference is that the edit buttons are on the left side instead of the right side.

## Requirements

- `php: >=7.3`
- `laravel/nova: ^3.0`

## Installation

Install the package in a Laravel Nova project via Composer:

```bash
# Install package
composer require cierrateam/nova-sortable
```

## Usage

### Create migration

Add an order field to the model using Laravel migrations:

```php
// Add order column to the model
Schema::table('some_model', function (Blueprint $table) {
  $table->integer('sort_order');
});

// Set default sort order (just copy ID to sort order)
DB::statement('UPDATE some_model SET sort_order = id');
```

### Implement eloquent-sortable

Implement the Spatie's `eloquent-sortable` interface and apply the trait:

```php
use Spatie\EloquentSortable\Sortable;
use Spatie\EloquentSortable\SortableTrait;

class SomeModel extends Eloquent implements Sortable
{
  use SortableTrait;

  public $sortable = [
    'order_column_name' => 'sort_order',
    'sort_when_creating' => true,
  ];

  ...
}
```

When the model does not have a sortable configuration, the default eloquent-sortable configuration will be used.

### Apply HasSortableRows to Nova resource

Apply `HasSortableRows` trait from this package on the Resource:

```php
use Cierrateam\NovaSortable\Traits\HasSortableRows;

class MyResource extends Resource
{
  use HasSortableRows;

  ...
}
```

NB! This overrides the `indexQuery()` method.

### Disallowing sorting on a per-request/resource basis

You can disable sorting on a per-request or per-resource basis by overriding the `canSort()` on the Resource method like so:

```php
public static function canSort(NovaRequest $request, $resource)
{
  // Do whatever here, ie:
  // return user()->isAdmin();
  // return $resource->id !== 5;
  return true;
}
```

## Sorting on HasMany relationship

**NB!** The resource can only be sorted on **either** the Index view **or** the HasMany list view, but not both!

Sorting on HasMany is simple. Add `'sort_on_has_many' => true` to the `$sortable` array on the model. Like so:

```php
public $sortable = [
  'order_column_name' => 'sort_order',
  'sort_when_creating' => true,
  'sort_on_has_many' => true,
];
```

The sort on has many configuration can be apply in a per model basis or it can be added in the eloquent-sortable configuration for all the models.

```php
return [

    // Spatie sortable configuration

    /**
     * Add sort on has many in all the models.
     **/
    'sort_on_has_many' => true,
];
```

## Sorting on ManyToMany relationships

Sorting on BelongsToMany and MorphToMany relationships is available, but requires special steps.

See the documentation here: [Sorting ManyToMany relationships (w/ pivot table)](docs/sorting/many-to-many.md).

## Localization

The translation file(s) can be published by using the following publish command:

```bash
php artisan vendor:publish --provider="Cierrateam\NovaSortable\ToolServiceProvider" --tag="translations"
```

You can add your translations to `resources/lang/vendor/nova-sortable/` by creating a new translations file with the locale name (ie `et.json`) and copying the JSON from the existing `en.json`.

## Credits

- [Tarvo Reinpalu](https://github.com/Tarpsvo)

## License

Nova Sortable is open-sourced software licensed under the [MIT license](LICENSE.md).
