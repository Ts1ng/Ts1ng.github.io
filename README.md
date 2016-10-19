**NOTE**:
> - Universal Links will not work if you paste the link into the browser URL field.
> - Universal Links work with a user driven ```<a href="...">``` element click across domains. Example: if there is a Universal * Link on google.com pointing to bnc.lt, it will open the app.
> - Universal Links will not work with a user driven ```<a href="...">``` element click on the same domain. Example: if there is a Universal Link on google.com pointing to a different Universal Link on google.com, it will not open the app.
> - Universal Links cannot be triggered via Javascript (in window.onload or via a .click() call on an ```<a>``` element), unless it is part of a user action.