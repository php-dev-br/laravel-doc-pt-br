# Eloquent: Collections

- [Introduction](#introduction)
- [Available Methods](#available-methods)
- [Custom Collections](#custom-collections)

<a name="introduction"></a>
## Introduction

All multi-result sets returned by Eloquent are instances of the `Illuminate\Database\Eloquent\Collection` object, including results retrieved via the `get` method or accessed via a relationship. The Eloquent collection object extends the Laravel [base collection](collections.md), so it naturally inherits dozens of methods used to fluently work with the underlying array of Eloquent models.

Of course, all collections also serve as iterators, allowing you to loop over them as if they were simple PHP arrays:

    $users = App\User::where('active', 1)->get();

    foreach ($users as $user) {
        echo $user->name;
    }

However, collections are much more powerful than arrays and expose a variety of map / reduce operations that may be chained using an intuitive interface. For example, let's remove all inactive models and gather the first name for each remaining user:

    $users = App\User::where('active', 1)->get();

    $names = $users->reject(function ($user) {
        return $user->active === false;
    })
    ->map(function ($user) {
        return $user->name;
    });

> {note} While most Eloquent collection methods return a new instance of an Eloquent collection, the `pluck`, `keys`, `zip`, `collapse`, `flatten` and `flip` methods return a [base collection](collections.md) instance. Likewise, if a `map` operation returns a collection that does not contain any Eloquent models, it will be automatically cast to a base collection.

<a name="available-methods"></a>
## Available Methods

### The Base Collection

All Eloquent collections extend the base [Laravel collection](collections.md) object; therefore, they inherit all of the powerful methods provided by the base collection class:

<style>
    #collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    #collection-method-list a {
        display: block;
    }
</style>

<div id="collection-method-list" markdown="1">

[all](collections.md#method-all)
[average](collections.md#method-average)
[avg](collections.md#method-avg)
[chunk](collections.md#method-chunk)
[collapse](collections.md#method-collapse)
[combine](collections.md#method-combine)
[contains](collections.md#method-contains)
[containsStrict](collections.md#method-containsstrict)
[count](collections.md#method-count)
[diff](collections.md#method-diff)
[diffKeys](collections.md#method-diffkeys)
[each](collections.md#method-each)
[every](collections.md#method-every)
[except](collections.md#method-except)
[filter](collections.md#method-filter)
[first](collections.md#method-first)
[flatMap](collections.md#method-flatmap)
[flatten](collections.md#method-flatten)
[flip](collections.md#method-flip)
[forget](collections.md#method-forget)
[forPage](collections.md#method-forpage)
[get](collections.md#method-get)
[groupBy](collections.md#method-groupby)
[has](collections.md#method-has)
[implode](collections.md#method-implode)
[intersect](collections.md#method-intersect)
[isEmpty](collections.md#method-isempty)
[isNotEmpty](collections.md#method-isnotempty)
[keyBy](collections.md#method-keyby)
[keys](collections.md#method-keys)
[last](collections.md#method-last)
[map](collections.md#method-map)
[mapWithKeys](collections.md#method-mapwithkeys)
[max](collections.md#method-max)
[median](collections.md#method-median)
[merge](collections.md#method-merge)
[min](collections.md#method-min)
[mode](collections.md#method-mode)
[only](collections.md#method-only)
[partition](collections.md#method-partition)
[pipe](collections.md#method-pipe)
[pluck](collections.md#method-pluck)
[pop](collections.md#method-pop)
[prepend](collections.md#method-prepend)
[pull](collections.md#method-pull)
[push](collections.md#method-push)
[put](collections.md#method-put)
[random](collections.md#method-random)
[reduce](collections.md#method-reduce)
[reject](collections.md#method-reject)
[reverse](collections.md#method-reverse)
[search](collections.md#method-search)
[shift](collections.md#method-shift)
[shuffle](collections.md#method-shuffle)
[slice](collections.md#method-slice)
[sort](collections.md#method-sort)
[sortBy](collections.md#method-sortby)
[sortByDesc](collections.md#method-sortbydesc)
[splice](collections.md#method-splice)
[split](collections.md#method-split)
[sum](collections.md#method-sum)
[take](collections.md#method-take)
[toArray](collections.md#method-toarray)
[toJson](collections.md#method-tojson)
[transform](collections.md#method-transform)
[union](collections.md#method-union)
[unique](collections.md#method-unique)
[uniqueStrict](collections.md#method-uniquestrict)
[values](collections.md#method-values)
[where](collections.md#method-where)
[whereStrict](collections.md#method-wherestrict)
[whereIn](collections.md#method-wherein)
[whereInStrict](collections.md#method-whereinstrict)
[zip](collections.md#method-zip)

</div>

<a name="custom-collections"></a>
## Custom Collections

If you need to use a custom `Collection` object with your own extension methods, you may override the `newCollection` method on your model:

    <?php

    namespace App;

    use App\CustomCollection;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Create a new Eloquent Collection instance.
         *
         * @param  array  $models
         * @return \Illuminate\Database\Eloquent\Collection
         */
        public function newCollection(array $models = [])
        {
            return new CustomCollection($models);
        }
    }

Once you have defined a `newCollection` method, you will receive an instance of your custom collection anytime Eloquent returns a `Collection` instance of that model. If you would like to use a custom collection for every model in your application, you should override the `newCollection` method on a base model class that is extended by all of your models.
