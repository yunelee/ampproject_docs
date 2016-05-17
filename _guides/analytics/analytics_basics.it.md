---
layout: page
title: Analytics&#58; i concetti di base
order: 0
locale: it
---

Inizia da qui per approfondire i concetti di base dell’analisi AMP.

{% include toc.html %}

## È meglio utilizzare amp-pixel o amp-analytics?

AMP offre due componenti per soddisfare le esigenze relative all’analisi e alla valutazione:
[amp-pixel](/docs/reference/amp-pixel.html) e
[amp-analytics](/docs/reference/extended/amp-analytics.html).
Entrambe le opzioni inviano dati di analisi a un endpoint predefinito.

Se sei alla ricerca di un comportamento simile a quello di un semplice
[pixel di tracciamento](https://en.wikipedia.org/wiki/Web_beacon#Implementation),
il componente `amp-pixel` offre il monitoraggio della visualizzazione di pagina di base
e l’invio dei dati sulle visualizzazioni a un URL specifico.
Alcune integrazioni con i fornitori possono richiedere questo componente,
in tal caso viene specificato l’esatto endpoint dell’URL. 

Per la maggior parte delle soluzioni di analisi puoi usare `amp-analytics`.
Il monitoraggio della visualizzazione di pagina funziona anche in `amp-analytics`.
Tuttavia, puoi anche monitorare il coinvolgimento dell’utente con qualsiasi tipo di contenuto di pagina,
compresi i clic sui link e i pulsanti.
Inoltre, puoi determinare di quanto è avanzato l’utente con lo scorrimento della pagina,
se l’utente è impegnato o meno sui social media e tanto altro ancora
(vedi
[Immersione nel mondo di AMP Analytics](/docs/guides/analytics/deep_dive_analytics.html)).

Nell’ambito dell’integrazione con la piattaforma AMP,
i fornitori hanno offerto configurazioni `amp-analytics` predefinite
per agevolare l’acquisizione di dati e l’invio ai rispettivi strumenti di monitoraggio.
Puoi accedere alla documentazione dei fornitori dalla
[specifica amp-analytics](/docs/reference/extended/amp-analytics.html).

Nelle tue pagine puoi usare sia `amp-pixel` che `amp-analytics`:
usa `amp-pixel` per il semplice monitoraggio delle visualizzazioni della pagina
e `amp-analytics` per tutto il resto.
Puoi anche aggiungere multipli di ogni tag.
Se collabori con più fornitori di soluzioni di analisi,
avrai bisogno di un tag per soluzione.
Tieni presente che le pagine AMP più semplici sono migliori per gli utenti,
pertanto se non hai necessità di tag extra, non usarli.

## Crea una configurazione di analisi semplice

Scopri come creare una configurazione
[amp-pixel](/docs/reference/amp-pixel.html) e
[amp-analytics](/docs/reference/extended/amp-analytics.html) semplice.

### Configurazione amp-pixel semplice

Per creare una configurazione `amp-pixel` semplice,
inserisci qualcosa di simile alla seguente istruzione nel corpo della tua pagina AMP:

{% highlight html %}
<amp-pixel src="https://foo.com/pixel?RANDOM"></amp-pixel>
{% endhighlight %}

In questo esempio,
i dati di visualizzazione di pagina vengono inviati all’URL definito, unitamente a un numero casuale.
La variabile `RANDOM` è una delle tante
[variabili di sostituzione della piattaforma AMP](https://github.com/ampproject/amphtml/blob/master/spec/amp-var-substitutions.md).
Per ulteriori informazioni sulla
[Sostituzione delle variabili](/docs/guides/analytics/analytics_basics.html#variable-substitution) vai qui.

Il componente [amp-pixel](/docs/reference/amp-pixel.html)
è incorporato,
pertanto non avrai necessità di una dichiarazione di inclusione come
nel caso dei componenti estesi di AMP, tra cui `amp-analytics`.
Tuttavia, dovresti posizionare il tag `amp-pixel` il più vicino possibile
all’inizio di `<body>`.
Il pixel di tracciamento si attiva solo quando si rende visibile il tag stesso.
È possibile che `amp-pixel` non si attivi se è posizionato vicino
alla parte finale della pagina.

### Configurazione amp-analytics semplice

Per cerare una configurazione
[amp-analytics](/docs/reference/extended/amp-analytics.html) semplice,
devi prima includere questa dichiarazione `custom-element`
nell’`<head>` del documento AMP (vedi anche
[Dichiarazione di inclusione del componente](/docs/reference/extended.html#component-inclusion-declaration)):

{% highlight html %}
<script async custom-element="amp-analytics" src="https://cdn.ampproject.org/v0/amp-analytics-0.1.js"></script>
{% endhighlight %}

Il seguente esempio è simile all’[esempio `amp-pixel`](/docs/guides/analytics/analytics_basics.html#simple-amp-pixel-configuration).
Ogni volta che una pagina è visibile,
l’evento di attivazione si attiva e
invia i dati sulla visualizzazione di pagina a un URL specifico unitamente a un ID casuale: 

{% highlight html %}
<amp-analytics>
<script type="application/json">
{
  "requests": {
    "pageview": "https://foo.com/pixel?RANDOM",
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

Nell’esempio sopra abbiamo definito una richiesta denominata pageview in modo che fosse https://foo.com/pixel?RANDOM. Come precedentemente indicato, RANDOM viene sostituito da un numero casuale, pertanto la richiesta finirà per avere il seguente aspetto https://foo.com/pixel?0.23479283687235653498734.

Quando la pagina diventa visibile
(come specificato dall’uso della parola chiave di attivazione `visible`),
si attiva un evento e viene inviata la richiesta `pageview`.
Gli attributi di attivazione determinano il momento in cui viene attivata la richiesta pageview.
Ottieni ulteriori informazioni su [richieste e attivazioni](/docs/guides/analytics/deep_dive_analytics.html#requests-triggers--transports).

## Sostituzione delle variabili

Entrambi i componenti [amp-pixel](/docs/reference/amp-pixel.html) e
[amp-analytics](/docs/reference/extended/amp-analytics.html)
consentono tutte le sostituzioni di variabili URL standard (vedi
[Sostituzioni di variabili HTML AMP](https://github.com/ampproject/amphtml/blob/master/spec/amp-var-substitutions.md)).
Nel seguente esempio,
la richiesta di visualizzazione di pagina viene inviata all’URL,
unitamente all’attuale URL canonico del documento AMP, al relativo titolo e a un 
[ID client](/docs/guides/analytics/analytics_basics.html#user-identification):

{% highlight html %}
<amp-pixel src="https://example.com/analytics?url=${canonicalUrl}&title=${title}&clientId=${clientId(site-user-id)}"></amp-pixel>
{% endhighlight %}

Grazie alla sua semplicità,
il tag `amp-pixel` può includere solo le variabili definite dalla piattaforma
o che la fase di runtime di AMP può analizzare dalla pagina AMP.
Nell’esempio sopra,
la piattaforma inserisce i valori sia per
`canonicalURL` che per `clientId(site-user-id)`.
Il tag `amp-analytics` può includere le stesse variabili di `amp-pixel`,
nonché variabili definite in modo univoco all’interno della configurazione dei tag.

Usa il formato `${varName}` in una stringa di richiesta per una variabile
definita da pagina o piattaforma.
Il `amp-analytics` tag sostituirà il modello con il suo valore effettivo
al momento della creazione della richiesta di analisi (vedi anche
[Variabili supportate in amp-analytics](https://github.com/ampproject/amphtml/blob/master/extensions/amp-analytics/analytics-vars.md)).

Nel seguente esempio di `amp-analytics`,
la richiesta di visualizzazione di pagina viene inviata all’URL,
con ulteriori dati estratti dalle sostituzioni delle variabili,
alcuni forniti dalla piattaforma,
altri definiti nella parte incorporata,
nell’ambito della configurazione `amp-analytics`:

{% highlight html %}
<amp-analytics>
<script type="application/json">
{
  "requests": {
    "pageview":"https://example.com/analytics?url=${canonicalUrl}&title=${title}&acct=${account}&clientId=${clientId(site-user-id)}",
  },
  "vars": {
    "account": "ABC123",
  },
  "triggers": {
    "someEvent": {
      "on": "visible",
      "request": "pageview",
      "vars": {
        "title": "My homepage",
      }
    }
  }  
}
</script>
</amp-analytics>
{% endhighlight %}

Nell’esempio sopra,
le variabili `account` e `title` sono definite
nella configurazione `amp-analytics`.
Le variabili `canonicalUrl` e `clientId` non sono definite nella configurazione,
pertanto i rispettivi valori vengono sostituiti dalla piattaforma.

**Importante** La sostituzione delle variabili è flessibile,
le stesse variabili possono essere definite in diverse posizioni
e la fase di runtime di AMP analizzerà i valori in questo ordine di precedenza
(vedi [Ordinamento della sostituzione delle variabili](/docs/guides/analytics/deep_dive_analytics.html#variable-substitution-ordering)).

## Identificazione dell’utente

I siti web usano i cookie per memorizzare le informazioni specifiche di un utente nel browser.
I cookie possono essere usati per indicare che un utente ha già visitato prima un sito.
In AMP,
le pagine possono essere distribuite dal sito web di un publisher o tramite una cache
(come la Google AMP Cache).
È probabile che il sito web del publisher e la cache abbiano domini diversi.
Per motivi di sicurezza,
i browser possono limitare l’accesso ai cookie di un altro dominio e spesso agiscono in tal senso
(vedi anche
[Monitoraggio degli utenti da diversi punti di origine](https://github.com/ampproject/amphtml/blob/master/extensions/amp-analytics/cross-origin-tracking.md)).

Per impostazione predefinita,
AMP gestisce l’assegnazione di un ID client sia nel caso di accesso alla pagina dal sito web originale dell’editore che tramite una cache.
L’ID client generato da AMP ha un valore di `"amp-"`
seguito da una stringa codificata `base64` casuale e resta lo stesso
per l’utente se lo stesso utente visita di nuovo la pagina.

AMP gestisce la lettura e la scrittura dell’ID client in tutti i casi.
Ciò è particolarmente evidente nel caso di una pagina distribuita
tramite una cache o altrimenti mostrata al di fuori del contesto di visualizzazione
del sito originale del publisher.
In tal caso non è possibile accedere ai cookie del sito del publisher.

Quando una pagina AMP viene distribuita dal sito di un publisher,
la struttura dell’ID client utilizzata da AMP può essere informata di un cookie di fallback
da cercare e utilizzare.
In questo caso,
l’argomento `cid-scope-cookie-fallback-name` della variabile `clientId`
viene interpretato come un nome di cookie.
La formattazione può presentarsi come
`CLIENT_ID(cid-scope-cookie-fallback-name)` o
`${clientId(cid-scope-cookie-fallback-name)}`.

Ad esempio:

{% highlight html %}
<amp-pixel src="https://foo.com/pixel?cid=CLIENT_ID(site-user-id-cookie-fallback-name)"></amp-pixel>
{% endhighlight %}

Se AMP rileva che questo cookie è impostato,
la sostituzione dell’ID client restituisce quindi il valore del cookie.
Se AMP rileva che questo cookie non è impostato,
AMP genera quindi un valore del modulo `amp-` seguito
da una stringa codificata base64 casuale.

Per ulteriori informazioni sulla sostituzione dell’ID client,
compreso il metodo per aggiungere un ID di notifica utente opzionale, vai alla sezione 
[Variabili supportate in AMP Analytics](https://github.com/ampproject/amphtml/blob/master/extensions/amp-analytics/analytics-vars.md).
