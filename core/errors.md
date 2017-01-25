# Error Handling

API Platform Core allows to customize the HTTP status code sent to the clients when exceptions are thrown.

```yaml
# app/config/config.yml

 services:
# Map exceptions to HTTP status codes using the `exception_to_status` configuration key
    exception_to_status:
        # The 2 following exceptions are handled by default
        Symfony\Component\Serializer\Exception\ExceptionInterface: 400 # Use a raw status code (recommended)
        ApiPlatform\Core\Exception\InvalidArgumentException: 'HTTP_BAD_REQUEST' # Or with a constant of `Symfony\Component\HttpFoundation\Response`

        AppBundle\Exception\ProductNotFoundException: 404 # Custom exceptions can easily be handled
```

As in any php application, your exceptions have to extends the \Exception class or any of it's children.

```php
<?php

// src/AppBundle/Exception/ProductNotFoundException.php

namespace AppBundle\Exception;

final class ProductNotFoundException extends \Exception
{
}
```

```php
<?php

// src/AppBundle/Manager/CartManager.php

namespace AppBundle\Manager;

use AppBundle\Entity\Product;
use AppBundle\Exception\ProductNotFoundException;

final class CartManager
{
    private const DOESNOTEXISTS = 51;
    private const DEACTIVATED = 52;
    private const OUTOFSTOCK = 53;

    /**
     * @param int $id
     *
     * @throws ProductNotFoundException
     */
    public function addProductToCart(int $id)
    {
        $product = /*...*/;

        if (!$product->getVirtualStock()) {
            // Using internal codes for a better understanding of what's going on
            throw new ProductNotFoundException(self::NOTFOUND_OUTOFSTOCK);
        }
    }
}
```

The exception doesn't have to be a Symfony's `HttpException`. Any type of `Exception` can be thrown. The best part is that API Platform already takes care of how the error is handled and returned. For instance the API is configured to respond in JSON-LD, the error will be returned in this format as well.

Previous chapter: [Pagination](pagination.md)

Next chapter: [The Event System](events.md)
