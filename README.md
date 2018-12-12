# UIowa Footer Module

Provides a theme render and simple UI to display custom information in the footer.

Out of the box, this module provides the "UIowa Standard" variant which satisfies the branding recommendations provided 
by the UI Office of Strategic Communication.

## Configuration

Configuration options for the appearance and content of the render are available on the core Basic Site Settings form at 
/admin/config/system/site-information.

## Custom variants

Additional variants my be provided by custom modules by following the steps below.
1. Add your custom variant to the list of options on the Basic Site Settings form by using hook_form_FORM_ID_alter().
    ```
    /**
     * Implements hook_form_FORM_ID_alter().
     */
    function mymodule_form_system_site_information_settings_alter(&$form, \Drupal\Core\Form\FormStateInterface $form_state, $form_id) {
      // Add your custom variant to the list of options.
      $form['uiowa_footer']['uiowa_footer_variant']['#options']['myvariant'] =  t('My Variant');
    }
    ```
    
    Depending on the weight of the modules, it may be necessary to implement `hook_module_implements_alter()` so that 
    the form alterations provided by the UIowa Footer module are implemented _before_ the alterations provided by your 
    custom module.
    ```
    /**
     * Implements hook_module_implements_alter().
     */
    function mymodule_module_implements_alter(&$implementations, $hook) {
      if ($hook === 'form_alter' && isset($implementations['mymodule'])) {
        // Move our form_alter to the end of the list so that it is called after
        // the form alterations provided by uiowa_footer.
        $group = $implementations['mymodule'];
        unset($implementations['mymodule']);
        $implementations['mymodule'] = $group;
      }
    }
    ```

2. Copy `uiowa_footer/templates/uiowa-footer.html.twig` to `mymodule/templates/uiowa-footer--myvariant.html.twig` and 
modify the new template as desired.

3. Register your custom template file with the theme system using `hook_theme()`.
    ```
    /**
     * Implements hook_theme.
     */
    function mymodule_theme($existing, $type, $theme, $path) {
      return [
        'uiowa_footer__myvariant' => [
          'base hook' => 'uiowa_footer',
        ],
      ];
    }
    ```
