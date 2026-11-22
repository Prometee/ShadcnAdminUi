# Sylius Shadcn Admin Ui

The Shadcn Admin Ui component is part of the [Sylius stack](https://github.com/Sylius/Stack) initiative.

## User menu avatar

The user menu displays the first two characters of the user's identifier by default. To display an image instead, extend the user dropdown component and resolve a URL for the authenticated user:

```php
namespace App\Twig\Component;

use App\Entity\AdminUser;
use Sylius\ShadcnAdminUi\Twig\Component\UserDropdownComponent as BaseUserDropdownComponent;
use Symfony\Component\Security\Core\User\UserInterface;

final class UserDropdownComponent extends BaseUserDropdownComponent
{
    protected function resolveAvatarUrl(UserInterface $user): ?string
    {
        return $user instanceof AdminUser ? $user->getAvatarUrl() : null;
    }
}
```

This example assumes your own `AdminUser::getAvatarUrl()` returns a nullable URL. Alternatively, resolve it using an application service. Replace the component service while preserving its arguments and Twig component tag:

```yaml
# config/services.yaml
services:
    sylius_shadcn_admin_ui.twig.component.navbar.user_dropdown:
        class: App\Twig\Component\UserDropdownComponent
        public: true
        arguments:
            - '%sylius_admin_ui.routing%'
            - '@security.token_storage'
            - '@router'
        tags:
            - { name: twig.component, key: 'sylius_shadcn_admin_ui:navbar:user_dropdown', template: '@SyliusShadcnAdminUi/shared/components/navbar/user.html.twig' }
```

Return an application-controlled URL. A null or empty URL keeps the initials; they also remain visible until the image loads and if it fails to load. The image is decorative because the user's identifier is already shown in the menu.
