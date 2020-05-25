```php

<?php

# src/Acme/Bundle/AppBundle/EventListener/ProductUpdateSubscriber.php

namespace Acme\Bundle\AppBundle\EventListener;

use Akeneo\Component\StorageUtils\StorageEvents;
use Pim\Component\Catalog\Model\ProductInterface;
use Symfony\Component\EventDispatcher\GenericEvent;
use Symfony\Component\EventDispatcher\EventSubscriberInterface;

class ProductUpdateSubscriber implements EventSubscriberInterface
{
    /**
     * {@inheritdoc}
     */
    public static function getSubscribedEvents() : array
    {
        // Extremely important to have your subscriber on top,
        // before raw values subscriber, otherwise any changes of
        // product data will be ignored
        return [
            StorageEvents::PRE_SAVE => ['updateProduct', 1000]
        ];
    }

    public function updateProduct(GenericEvent $event)
    {
        $object = $event->getSubject();

        if (!$object instanceof ProductInterface) {
            return;
        }

        // do your stuff here
    }
}

```    
