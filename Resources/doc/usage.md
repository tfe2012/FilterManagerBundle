# Using filter manager

## Using controller

### Using default controller
Once you set up your [*managers*](manager.md) you don't need to create a controller for each one,
you can just use default manager controller `ONGRFilterManagerBundle:Manager:manager`.

Example:
```yaml
#src/Acme/DemoBundle/Resources/config/routing.yml
ongr_search_page:
    pattern: /list
    methods:  [GET]
    defaults:
        _controller: ONGRFilterManagerBundle:Manager:manager
        managerName: "item_list"
        template: "AcmeDemoBundle:List:results.html.twig"
```
This specific example will render template `AcmeDemoBundle:List:results.html.twig`,
with [SearchResponse][1] from [FiltersManager][2] named `item_list`.

### Using custom controller.
You can still use custom controller by getting your needed manager from the container.
This way you can add your custom variables if needed.

Example:
```yaml
#src/Acme/DemoBundle/Resources/config/routing.yml
ongr_search_page:
    pattern: /list
    methods:  [GET]
    defaults:
        _controller: AcmeDemoBundle:List:index
```

```php
//src/Acme/DemoBundle/Controller/ListController.php
/**
 * Controller for list pages.
 */
class ListController extends Controller
{
    /**
     * Renders my list page
     *
     * @param Request $request
     *
     * @return Response
     */
    public function indexAction(Request $request)
    {
        return $this->render(
            'AcmeDemoBundle:List:results.html.twig',
            [
                'filter_manager' => $this->getProductsData($request),
                'my_custom_variable' => $var,
            ]
        );
    }
    
    /**
     * Returns item list
     *
     * @param Request $request
     *
     * @return array
     */
    private function getProductsData($request)
    {
        //here we get our filter manager
        return $this->get('ongr_filter_manager.item_list')->execute($request);
    }
}
```

## Template variables
If you're using default controller, [SearchResponse][1] from [FiltersManager][2] will be named `filter_manager` in template,
otherwise it's whatever you call it in your controller.

You can use [SearchResponse][1] to get results in your template.
Example:
```
{% for item in filter_manager.result %}
    <b>{{ item.title }}</b>
{% endfor %}
```

You can also use it to get data about your filter.
Example:
```
Pager url parameters: {{ filter_manager.filters.pager.getUrlParameters() }}
```

A complete list of parameters for each filter can be found in its documentation:

* [Choice filter](filter/choice.md)
* [Document field filter](filter/document_field.md)
* [Match filter](filter/match.md)
* [Pager filter](filter/pager.md)
* [Sort filter](filter/sort.md)

[1]: https://github.com/ongr-io/FilterManagerBundle/blob/master/Search/SearchResponse.php
[2]: https://github.com/ongr-io/FilterManagerBundle/blob/master/Search/FiltersManager.php
