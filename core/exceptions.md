# Exceptions

API Platform Core allows you to customize the HTTP status code sent after an exception is thrown.

## configuration

```YAML
# app/config/config.yml

 api_platform:

    # ...

    exception_to_status:
        # By default there are two exceptions defined that you can customize.

        # With a status code (recommended).
        Symfony\Component\Serializer\Exception\ExceptionInterface: 400

        # Or with a constant defined in the 'Symfony\Component\HttpFoundation\Response' class.
        ApiPlatform\Core\Exception\InvalidArgumentException: 'HTTP_BAD_REQUEST'

        # You can then add yours
        Foo\Bar\BazException: 401

        # ...
```

## Custom Exception

As in any php application, your exceptions have to extends the \Exception class or any of it's children.

```PHP
<?php

namespace AppBundle\Exception;

/**
 * Class ProductNotFoundException
 */
class ProductNotFoundException extends \Exception
{

    public function __construct($code = 0) {
        parent::__construct("Product Not found", $code);
    }
}
```

```PHP
<?php

namespace AppBundle\Manager;

use AppBundle\Entity\Product;
use AppBundle\Exception\ProductNotFoundException;

/**
 * Class CartManager
 */
class CartManager
{
    const NOTFOUND_DOESNOTEXISTS = 51;
    const NOTFOUND_DEACTIVATED = 52;
    const NOTFOUND_OUTOFSTOCK = 53;

    /**
     * @param integer $id
     *
     * @throws ProductNotFoundException
     *
     * @return Product $product
     */
    public function addProductToCart($id)
    {
        $product = /*...*/;

        if (!$product->getVirtualStock()) {
            // Using internal codes for a better understanding of what's going on
            throw new ProductNotFoundException(self::NOTFOUND_OUTOFSTOCK);
        }

        /*...*/

        return $cart;
    }
}
```

It doesn't have to be an HttpException, any type of Exception can be thrown. The best part is that API Platform already takes care of how the error is handled and returned. If you configured your API to respond in a JsonLD format, your error will be returned in JsonLD format as well.

Previous chapter: [Pagination](pagination.md)

Next chapter: [The Event System](events.md)
