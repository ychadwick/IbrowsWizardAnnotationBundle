IbrowsWizardAnnotationBundle
============================

Give's a Symfony2 controller a simple wizard/workflow with annotations.

How to install
==============

### Add Bundle to your composer.json

```js
// composer.json

{
    "require": {
        "ibrows/wizard-annotation-bundle": "*"
    }
}
```

### Install the bundle from console with composer.phar

``` bash
$ php composer.phar update ibrows/wizard-annotation-bundle
```

### Enable the bundle in AppKernel.php

``` php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new Ibrows\Bundle\WizardAnnotationBundle\IbrowsWizardAnnotationBundle(),
    );
}
```

### Using in a controller

``` php
<?php

namespace YourApp\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Symfony\Component\HttpFoundation\RedirectResponse;

use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Template;

use Ibrows\Bundle\WizardAnnotationBundle\Annotation\Wizard;

/**
 * @Route("/wizard")
 */
class WizardController extends Controller
{
    /**
     * @Route("/register", name="wizard_register")
     * @Template
     * @Wizard(name="register", number=1)
     * @return array
     */
    public function registerAction()
    {
        return array();
    }

    /**
     * @Route("/adress", name="wizard_adress")
     * @Template
     * @Wizard(name="adress", number=2, validationMethod="adressValidation")
     * @return array
     */
    public function adressAction()
    {
        return array();
    }

    /**
     * @return bool
     */
    public function adressValidation()
    {
        /**
         * Do your checks if this step is valid and return a Response/RedirectResponse or Wizard::REDIRECT_STEP_BACK
         * if something is wrong. Otherwise return true
         */

        if(!$this->getUser()){
            return Wizard::REDIRECT_STEP_BACK;
        }

        return true;
    }

    /**
     * @Route("/summary", name="wizard_summary")
     * @Template
     * @Wizard(name="summary", number=3, validationMethod="summaryValidation")
     * @return array
     */
    public function summaryAction()
    {
        return array();
    }

    /**
     * @return bool
     */
    public function summaryValidation()
    {
        /**
         * Do your checks if this step is valid and return a Response/RedirectResponse or Wizard::REDIRECT_STEP_BACK
         * if something is wrong. Otherwise return true
         */

        if(!$this->someCheck()){
            return new RedirectResponse($this->generateUrl('index'));
        }

        if(!$this->someOtherCheck()){
            return new RedirectResponse($this->generateUrl('login'));
        }

        return true;
    }

    /**
     * @Route("/display", name="wizard_display")
     * @Template
     * @return array
     */
    public function displayAction()
    {
        return array(
            'wizard' => $this->get('ibrows_wizardannotation.annotation.handler')
        );
    }
}
```

### Display the wizard

Just render the wizardAction in the view:

``` html
    {% render "YourBundle:Wizard:display" %}
```

And the diplay.html.twig:

``` html
    {% extends 'IbrowsWizardAnnotationBundle:Wizard:base.html.twig' %}
```