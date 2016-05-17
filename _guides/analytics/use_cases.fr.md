---
layout: page
title: Cas d'utilisation
order: 2
locale: fr
---

Ce guide propose des cas d'utilisation courants pour suivre l'engagement des utilisateurs :

{% include toc.html %}

Vous aimeriez ajouter un cas d'utilisation ? 
[Dites-le nous.](https://github.com/ampproject/docs/issues/new)

Vous pouvez également partager vos propres cas d'utilisation ;
voir [Comment contribuer](https://www.ampproject.org/docs/support/contribute.html).

## Suivi des vues de page

Découvrez comment suivre les vues de page avec `amp-pixel` et `amp-analytics`. 

### Utilisation du composant amp-pixel

Envoyez des données sur les vues de page à une URL spécifiée
en utilisant [amp-pixel](/docs/reference/amp-pixel.html) :

{% highlight html %}
<amp-pixel src="https://foo.com/pixel?"></amp-pixel>
{% endhighlight %}

### Utilisation du composant amp-analytics (pas de fournisseur)

Envoyez des données sur les vues de page à une URL spécifiée
en utilisant [amp-analytics](/docs/reference/extended/amp-analytics.html) :

{% highlight html %}
<amp-analytics>
<script type="application/json">
{
  "requests": {
    "pageview": "https://example.com/analytics?url=${canonicalUrl}&title=${title}&acct=${account}"
  },
  "vars": {
    "account": "ABC123"
  },
  "triggers": {
    "trackPageview": {
      "on": "visible",
      "request": "pageview"
    }
  }
}
</script>
</amp-analytics>
{% endhighlight %}

### Utilisation du composant amp-analytics (googleanalytics)

Envoyez des données sur les vues de page à Google Analytics
(voir également [Suivi des pages dans Google Analytics](https://developers.google.com/analytics/devguides/collection/amp-analytics/#page_tracking)) : 

{% highlight html %}
<amp-analytics type="googleanalytics" id="analytics1">
<script type="application/json">
{
  "vars": {
    "account": "UA-XXXXX-Y"  // Replace with your property ID.
  },
  "triggers": {
    "trackPageview": {  // Trigger names can be any string. trackPageview is not a required name.
      "on": "visible",
      "request": "pageview"
    }
  }
}
</script>
</amp-analytics>
{% endhighlight %}

## Suivi des clics sur une page

Découvrez comment suivre les clics sur une page à l'aide de 
[amp-analytics](/docs/reference/extended/amp-analytics.html)
en envoyant les données d'événement à une URL spécifiée et à
[Google Analytics](https://developers.google.com/analytics/devguides/collection/amp-analytics/).

### Envoi de données à une URL spécifiée

L'exemple suivant utilise l'attribut `selector` pour envoyer un événement `click`
à l'URL spécifiée à chaque fois qu'un utilisateur clique sur un lien (`<a href>`) :

{% highlight html %}
<amp-analytics>
<script type="application/json">
{
  "requests": {
    "event": "https://example.com/analytics?eid=${eventId}&elab=${eventLabel}&acct=${account}"
  },
  "vars": {
    "account": "ABC123"
  },
  "triggers": {
    "trackAnchorClicks": {
      "on": "click",
      "selector": "a",
      "request": "event",
      "vars": {
        "eventId": "42",
        "eventLabel": "clicked on a link"
      }
    }
  }
}
</script>
</amp-analytics>
{% endhighlight %}

### Envoi de données à Google Analytics

L'exemple suivant utilise l'attribut `selector` de `trigger`
pour envoyer un événement `click` à Google Analytics lorsque l'on clique sur un élément particulier
(voir également
[Suivi des événements AMP dans Google Analytics](https://developers.google.com/analytics/devguides/collection/amp-analytics/#event_tracking)) :

{% highlight html %}
<amp-analytics type="googleanalytics" id="analytics3">
<script type="application/json">
{
  "vars": {
    "account": "UA-XXXXX-Y"  // Replace with your property ID.
  },
  "triggers": {
    "trackClickOnHeader" : {
      "on": "click",
      "selector": "#header",
      "request": "event",
      "vars": {
        "eventCategory": "ui-components",
        "eventAction": "header-click"
      }
    }
  }
}
</script>
</amp-analytics>
{% endhighlight %}

## Suivi du défilement

Suivez le défilement des pages avec [amp-analytics](/docs/reference/extended/amp-analytics.html).
L'exemple suivant utilise l'attribut `scrollspec` pour envoyer un événement `scroll`
à l'URL spécifiée à chaque fois qu'un utilisateur fait défiler verticalement la page de 25, 50 et 90 %.
Cet événement se déclenche également lorsque l'on fait défiler
horizontalement la page sur 90 % de sa largeur `scroll` :

{% highlight html %}
<amp-analytics>
<script type="application/json">
{
  "requests": {
    "event": "https://example.com/analytics?eid=${eventId}&elab=${eventLabel}&acct=${account}"
  },
  "vars": {
    "account": "ABC123"
  },
  "triggers": {
    "scrollPings": {
      "on": "scroll",
      "scrollSpec": {
        "verticalBoundaries": [25, 50, 90],
        "horizontalBoundaries": [90]
      }
    }
  }
}
</script>
</amp-analytics>
{% endhighlight %}

## Suivi des interactions sociales

Découvrez comment suivre les interactions sociales à l'aide de 
[amp-analytics](/docs/reference/extended/amp-analytics.html)
en envoyant les données d'événement à une URL spécifiée et à
[Google Analytics](https://developers.google.com/analytics/devguides/collection/amp-analytics/).

### Envoi de données à une URL spécifiée

L'exemple suivant utilise l'attribut `selector` pour envoyer un événement `click`
à l'URL spécifiée à chaque fois qu'un utilisateur clique sur un tweet (« #tweet-link ») :

{% highlight html %}
<amp-analytics>
<script type="application/json">
{
  "requests": {
    "event": "https://example.com/analytics?eid=${eventId}&elab=${eventLabel}&acct=${account}"
  },
  "vars": {
    "account": "ABC123"
  },
  "triggers": {
    "trackClickOnTwitterLink": {
      "on": "click",
      "selector": "#tweet-link",
      "request": "event",
      "vars": {
        "eventId": "43",
        "eventLabel": "clicked on a tweet link"
      }
    }
  }
}
</script>
</amp-analytics>
{% endhighlight %}

### Envoi de données à Google Analytics

L'exemple suivant utilise l'attribut `selector` de `trigger`
pour envoyer un événement lorsque l'on clique sur un bouton de réseau social particulier
(voir également
[Suivi des interactions sociales AMP dans Google Analytics](https://developers.google.com/analytics/devguides/collection/amp-analytics/#social_interactions)) :

{% highlight html %}
<amp-analytics type="googleanalytics" id="analytics4">
<script type="application/json">
{
  "vars": {
    "account": "UA-XXXXX-Y" // Replace with your property ID.
  },
  "triggers": {
    "trackClickOnTwitterLink" : {
      "on": "click",
      "selector": "#tweet-link",
      "request": "social",
      "vars": {
          "socialNetwork": "twitter",
          "socialAction": "tweet",
          "socialTarget": "https://www.examplepetstore.com"
      }
    }
  }
}
</script>
</amp-analytics>
{% endhighlight %}
