---
layout: page
title: AMP Analytics Derinlemesine Giriş
order: 1
locale: tr
---

Bu kılavuz 
[amp-analitik bileşen](/docs/reference/extended/amp-analytics.html) içerisine derinlemesine girerek, bir örnek 
`amp-analytics` yapılandırmasını temel yapıtaşlarına ayırır:

{% include toc.html %}

Kılavuzun kalan kısmında bu yapılandırma örneği kullanılarak,
sayfa görünümeleri ve kullanıcının bağlantı tıklamaları izlenir
 ve analitik veriler üçüncü şahıs sağlayıcıya,
[Google Analytics](https://developers.google.com/analytics/devguides/collection/amp-analytics/) gönderilir:

{% highlight html %}
<amp-analytics type="googleanalytics" config="https://example.com/analytics.account.config.json">
<script type="application/json">
{
  "requests": {
    "pageview": "https://example.com/analytics?url=${canonicalUrl}&title=${title}&acct=${account}",
    "event": "https://example.com/analytics?eid=${eventId}&elab=${eventLabel}&acct=${account}"
  },
  "vars": {
    "account": "ABC123"
  },
  "extraUrlParams": {
    "cd1": "AMP"
  },
  "triggers": {
    "trackPageview": {
      "on": "visible",
      "request": "pageview"
    },
    "trackAnchorClicks": {
      "on": "click",
      "selector": "a",
      "request": "event",
      "vars": {
        "eventId": "42",
        "eventLabel": "clicked on a link"
      }
    }
  },
  'transport': {
    'beacon': false,
    'xhrpost': false,
    'image': true
  }
}
</script>
</amp-analytics>
{% endhighlight %}

**Not:** Yukarıdaki örnek kod öğrenmenize yardımcı olmak için verilmiştir, gerçek bir örnek değildir. Analitik sağlayıcılarla çalışıyorsanız, yukarıdaki örnek sizin için bir anlam ifade etmeyebilir; sağlayıcı yapılandırmaları karmaşıklığı ortadan kaldırır. Örnek yapılandırmalar için analitik sağlayıcınızın belgelerine başvurun.

## Analitik verilerin gönderileceği yer: yazma özelliği

AMP, iki yaygın veri toplama şeklini destekleyecek şekilde tasarlanmıştır:

* Kurum içi analitik sistemler için yayıncıya ait son nokta ile alınım.
* Bir satıcı çözümü ile birlikte işlerlik için satıcıya ait bir son nokta ile alınım
(örneğin, [Adobe Analytics](https://helpx.adobe.com/marketing-cloud/analytics.html), [Chartbeat](http://support.chartbeat.com/docs/), [Google Analytics](https://developers.google.com/analytics/devguides/collection/amp-analytics/)).

Analitik verileri bir analitik sağlayıcıya göndermek için,
`amp-analytics` etiketine `type` özelliği ekleyin ve 
[amp-analitik spesifikasyonu](/docs/reference/extended/amp-analytics.html) içerisinde belirlenen şekilde değerini 
ilgili satıcıya ayarlayın.  

Örneğin: `<amp-analytics type="googleanalytics">` analitik verileri 
üçüncü şahıs analitik sağlayıcı, Google Analytics›e gönderir.
Verileri yayıncıya ait bir son noktaya göndermek için,
`type` özelliğini eklememeniz yeterlidir;
analitik veriler her bir 
[istek](/docs/guides/analytics/deep_dive_analytics.html#what-data-gets-sent-requests-attribute)için tanımlanan son noktalara gönderilir.

Analytics satıcı yapılandırmaları `amp-analytics`‹e başlamanın 
hızlı bir yoludur.
Ayrıntılı rehberlik için satıcı belgelerine ve 
yardım kaynaklarına danışmanız gerekir.
Daha önce bahsedildiği gibi,
halihazırda AMP ile entegre ettiğiniz satıcıların listesi, aynı zamanda 
spesifik belgelerin bağlantıları 
[amp-analitik spesifikasyonu](/docs/reference/extended/amp-analytics.html) içerisinde bulunabilir. 

Bir analitik satıcıysanız,

[AMP HTML›sine kendi analitik yapılandırmanızı entegre etme](https://github.com/ampproject/amphtml/blob/master/extensions/amp-analytics/integrating-analytics.md) hakkında daha fazla öğrenin.

## Uzaktan yapılandırma yükle: yapılandırma özelliği

AMP sayfanıza `amp-analytics` yapılandırmasının
tamamını eklemeniz gerekli değildir.
Bunun yerine, yapılandırmaların tamamı ya da bir kısmı için
uzaktan bir URL talep edebilirsiniz.

Bu şekilde, belli bir isteğe göre
yapılandırmayı değiştirme gibi işlemleri yapabilirsiniz.
Yayıncı olarak, uzaktan dosya üzerinde kontrolünüz varsa,
yapılandırma verilerini şekillendirmek için gerekli
sunucu tarafı işlemlerinin tümünü gerçekleştirebilirsiniz.
 
Uzaktan yapılandırmaları yüklemenin ilk adımı
`amp-analytics` etiketine yapılandırma özelliğinin eklenmesidir:

{% highlight html %}
<amp-analytics config="https://example.com/analytics.account.config.json">
{% endhighlight %}

Bir sonraki adım, uzaktan URL içerisinde yer alan JSON içeriğinin oluşturulmasıdır.
Bu basit örnekte,
JSON nesnesi içerisinde yer alan yapılandırma, analitik hesabının yalnızca değişken değeridir.

`https://example.com/analytics.account.config.json`‹deki örnek içeriği:

{% highlight html %}
{
  "vars": {
    "account": "UA-XXXXX-Y"  // Replace with your property ID.
  }
}
{% endhighlight %}

Son adım, uzaktan dosya içindekilerin
 `amp-analytics` yapılandırmasında uygun yerlere çekilmesidir.
Buradaki hem `pageview` hem de `event` isteklerinde,
`account` değişken değeri 
uzaktan URL (`"account": "UA-XXXXX-Y"`) içindeki hesap değerine otomatik olarak ayarlanır:

{% highlight html %}
"requests": {
  "pageview": "https://example.com/analytics?url=${canonicalUrl}&title=${title}&acct=${account}",
  "event": "https://example.com/analytics?eid=${eventId}&elab=${eventLabel}&acct=${account}"
}
{% endhighlight %}

**Önemli:** AMP aynı değişkenin birden çok kullanımını doğrulamaz.
Değerler, önceliğe göre değişken değiştirme sırasına göre çoğaltılır
ve uzaktan URL›lerin içindeki değerler bu sıranın en üzerindedir 
(bkz. [Değişken değiştirme sıralaması](/docs/guides/analytics/deep_dive_analytics.html#variable-substitution-ordering)).

## İstek, tetikleme ve taşıma

`requests` özelliği ‹hangi verilerin gönderileceğini›
(örneğin, `pageviews`, `events`)
ve bu verilerin nereye gönderileceğini tanımlar (verileri aktarmak için kullanılan URL›ler).

`triggers` özelliği analitik verilerinin ne zaman gönderileceğini;
örneğin, kullanıcının bir sayfayı ne zaman görüntülediğini, bir bağlantıya ne zaman tıkladığını tanımlar. 

`transport` özelliği bir isteğin,
daha spesifik olarak protokolün nasıl gönderileceğini belirler. 

Bu yapılandırmalar hakkında daha fazla bilgi için okumaya devam edin.
(Bu yapılandırmalar hakkında bilgi için 
[amp-analitik referansı](/docs/reference/extended/amp-analytics.html) bölümünü de okuyabilirsiniz.)

### Hangi veriler gönderilir: istek özelliği

`request-name` tetikleme yapılandırmasında 
belli bir etkinliğe karşı hangi isteğin gönderileceğini belirlemek için kullanılır.
`request-value` bir `https` URL›sidir.
Bu değerler, 
diğer istek veya değişkenlere referans verebilen yer tutucu işaretlerini içerebilir.

{% highlight html %}
"requests": {
  "pageview": "https://example.com/analytics?url=${canonicalUrl}&title=${title}&acct=${account}",
  "event": "https://example.com/analytics?eid=${eventId}&elab=${eventLabel}&acct=${account}"
}
{% endhighlight %}

Bazı analitik sağlayıcıları (Google Analytics dahil)
 halihazırda `type` özelliği aracılığıyla
kullandığınız yapılandırma sağlamıştır.
Bir analitik sağlayıcı kullanıyorsanız,
 `requests` bilgilerini eklemeniz gerekli olmayabilir.

`requests` yapılandırması gerekli olup olmadığını ve nasıl yapılacağını öğrenmek için satıcı belgelerine başvurun.

#### İstek URL›si ekleme: Ekstra URL Parametreleri

[extraUrlParams](https://github.com/ampproject/amphtml/blob/master/extensions/amp-analytics/amp-analytics.md#extra-url-params)
özelliği normal "&foo=baz" kuralı aracılığıyla istek URL›sinin sorgu dizesine eklenecek ek parametreleri belirler.

`amp-analytics` özelliği isteğe ek bir <code>cd1</code> parametresi 
ekler ve parametre değerini «AMP» olarak ayarlar:

{% highlight html %}
  "extraUrlParams": {
    "cd1": "AMP"
  }
{% endhighlight %}

### Veri ne zaman gönderilir: tetikleme özelliği

`triggers` özelliği bir analitik isteğinin ne zaman gönderilmesi gerektiğini tanımlar.
Tetikleme adı ve tetikleme yapılandırmasının bir anahtar değer çiftini içerir.
Tetikleme adı alfanümerik karakterlerden (a-zA-Z0-9) oluşan
herhangi bir dize olabilir. 

Örneğin,
aşağıdaki `amp-analytics` ögesi 
belge ilk yüklendiğinde 
ve bir `a` etiketine her tıklandığında `https://example.com/analytics`‹e bir istek gönderilecek şekilde yapılandırılmıştır:

{% highlight html %}
"triggers": {
  "trackPageview": {
    "on": "visible",
    "request": "pageview"
  },
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
{% endhighlight %}

AMP aşağıdaki tetikleme yapılandırmalarını destekler:

<table>
  <thead>
    <tr>
      <th data-th="Trigger Config" class="col-thirty">Tetikleme Yapılandırması</th>
      <th data-th="Description">Tanım</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-th="Trigger Config"><code>on</code> (gerekli)</td>
      <td data-th="Description">Dinleyici etkinliği. Geçerli değerler <code>click</code>, <code>scroll</code>, <code>timer</code> ve<code>visible</code>‹dır.</td>
    </tr>
    <tr>
      <td data-th="Trigger Config"><code>request</code> (gerekli)</td>
      <td data-th="Description">Gönderilecek isteğin adı (<a href="/docs/guides/analytics/deep_dive_analytics.html#what-data-gets-sent-requests-attribute">isteklerinde belirtilen şekilde</a>).</td>
    </tr>
    <tr>
      <td data-th="Trigger Config"><code>vars</code></td>
      <td data-th="Description">En üst seviye yapılandırmada tanımlanan <code>vars</code> geçersiz kılmak veya bu tetiklemeye özgü<code>vars</code> belirlemek için anahtar değer çiftlerini içeren bir nesne (ayrıca bkz. <a href="/docs/guides/analytics/deep_dive_analytics.html#variable-substitution-ordering">Değişken değiştirme sıralaması</a>).</td>
    </tr>
    <tr>
      <td data-th="Trigger Config"><code>selector</code> (<code>on</code>, <code>click</code> olarak ayarlandığında gereklidir)</td>
      <td data-th="Description">Hangi ögelerin seçilmesi gerektiğini işlemek için kullanılan bir CSS selektörü. Tüm ögeleri izlemek için <code>*</code> değerini kullanın. Bu yapılandırma <code>click</code> tetikleme ile birlikte kullanılır. Selektörü kullanarak <a href="/docs/guides/analytics/use_cases.html#tracking-page-clicks">sayfa tıklamalarını </a> ve<a href="/docs/guides/analytics/use_cases.html#tracking-social-interactions"> sosyal medya etkileşimlerini nasıl izleyeceğinizi öğrenin</a>.</td>
    </tr>
    <tr>
      <td data-th="Trigger Config"><code>scrollSpec</code> (<code>on</code>, <code>scroll</code> olarak ayarlandığında gereklidir)</td>
      <td data-th="Description">Hangi koşullar altında sayfa kaydırıldığında <code>scroll</code> etkinliğinin uyarı verdiğini kontrol eder. Bu nesne <code>verticalBoundaries</code> ve<code>horizontalBoundaries</code> içerebilir. Bir <code>scroll</code> etkinliğinin uyarı vermesi için iki özellikten en az biri gereklidir. Her iki özelliğin değerleri bir kaydırma etkinliğinin oluşturulduğu sınırları içeren sayılar dizini olmalıdır. <a href="/docs/guides/analytics/use_cases.html#tracking-scrolling">kaydırma izleme</a> ile ilgili örneğe bakın.</td>
    </tr>
    <tr>
      <td data-th="Trigger Config"><code>timerSpec</code> (<code>on</code>, <code>timer</code> olarak ayarlandığında gereklidir)</td>
      <td data-th="Description"><code>timer</code> etkinliğinin ne zaman uyarı vereceğini kontrol eder. Zamanlayıcı anında ve ardından belirlenen bir zaman aralığında tetiklenir. Bu yapılandırma <code>timer</code> tetikleme ile birlikte kullanılır.</td>
    </tr>
  </tbody>
</table>

**Önemli:** Düşük öncelikli bir yapılandırmadaki tetiklemeler,
yüksek öncelikli yapılandırmadaki aynı adlı tetiklemelerle geçersiz kılınır
(bkz. [Değişken değiştirme sıralaması](/docs/guides/analytics/deep_dive_analytics.html#variable-substitution-ordering)).

### Verileri gönderme: aktarma özelliği

`transport` özelliği bir isteğin nasıl gönderileceğini tanımlar.
Aşağıdaki üç yöntem varsayılan olarak etkinleştirilir:

<table>
  <thead>
    <tr>
      <th data-th="Transport Method" class="col-thirty">Taşıma Yöntemi</th>
      <th data-th="Description">Tanım</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-th="Transport Method"><code>beacon</code></td>
      <td data-th="Description"><a href="https://developer.mozilla.org/en-US/docs/Web/API/Navigator/sendBeacon">navigator.sendBeacon</a> isteği aktarmak için kullanılabilir. Bu kimlik bilgileri ile birlikte bir <code>POST</code> isteği ve boş bir gövde gönderir.</td>
    </tr>
    <tr>
      <td data-th="Transport Method"><code>xhrpost</code></td>
      <td data-th="Description"><code>XMLHttpRequest</code> isteği aktarmak için kullanılabileceğini gösterir. Bu kimlik bilgileri ile birlikte bir <code>POST</code> isteği ve boş bir gövde gönderir.</td>
    </tr>
    <tr>
      <td data-th="Transport Method"><code>image</code></td>
      <td data-th="Description">İsteğin bir <code>Image</code> etiketi oluşturarak gönderilebileceğini gösterir. Bu bir <code>GET</code> isteği gönderecektir.</td>
    </tr>
  </tbody>
</table>

Yalnızca bir aktarma yöntemi kullanılır,
bu da etkinleştirilen, izin verilen ve kullanılabilir
en yüksek önceliğe sahip yöntemdir.
Öncelik `beacon` > `xhrpost` > `image` şeklindedir.
İstemci kullanıcı aracısı bir yöntemi desteklemiyorsa,
etkinleştirilen en yüksek yöntem kullanılır. 

`transport` özelliğini yapılandırmanıza 
yalnızca taşıma seçeneklerini sınırlamak istediğinizde ekleyin,
aksi halde, istekleri durdurabilirsiniz.

Aşağıdaki örnekte,
`beacon` ve `xhrpost` yanlış olarak ayarlanmıştır,
bu nedenle `image`‹den daha yüksek önceliğe sahip olsalar da kullanılmazlar.
İstemcinin kullanıcı aracısı `image` yöntemini destekliyorsa,
bu yöntem kullanılır; aksi halde hiçbir istek gönderilmez.

{% highlight html %}
'transport': {
  'beacon': false,
  'xhrpost': false,
  'image': true
}
{% endhighlight %}

## Değişken değiştirme sıralaması

AMP bir öncelik sırasına göre değişkenleri değerlerle doldurur:

1. Uzaktan yapılandırmalar (`config` aracılığıyla).
2. `vars`, `triggers` içerisinde bir tetikleme içinde yuvalanmıştır.
3. `amp-analytics` içerisinde en üst seviyede yuvalanan `vars`.
4. Platform tarafından sağlanan değerler.

Bu örnekte, uzaktan bir yapılandırma vardır,
değişkenler en üst seviyede, tetiklemelerde ve platform seviyesinde tanımlanmıştır:

{% highlight html %}
<amp-analytics config="http://example.com/config.json">
<script type="application/json">
{
  "requests": {
    "pageview": "https://example.com/analytics?url=${canonicalUrl}&title=${title}&acct=${account}&clientId=${clientId(cid-scope)}",
  },
  "vars": {
    "account": "ABC123",
    "title": "Homepage"
  },
  "triggers": {
    "some-event": {
      "on": "visible",
      "request": "pageview",
      "vars": {
        "title": "My homepage",
        "clientId": "my user"
      }
  }
}
</script>
</amp-analytics>
{% endhighlight %}

Aynı `var` birden fazla yerde tanımlanmışsa,
değişkenin öncelik sırası değerini bir kez belirler.
Dolayısıyla, uzaktan yapılandırma yukarıdaki örnekte`account` UA-XXXXX-Y olarak tanımlandıysa,
çeşitli değişkenlerin değerleri aşağıdaki gibi olacaktır:

<table>
  <thead>
    <tr>
      <th data-th="var" class="col-thirty"><code>var</code></th>
      <th data-th="Value">Değer</th>
      <th data-th="Defined By" class="col-thirty">Tanımlanan</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-th="var"><code>canonicalUrl</code></td>
      <td data-th="Value"><code>http://example.com/path/to/the/page</code></td>
      <td data-th="Defined By">Platform</td>
    </tr>
    <tr>
      <td data-th="var"><code>title</code></td>
      <td data-th="Value">My homepage</td>
      <td data-th="Defined By">Tetikleme</td>
    </tr>
    <tr>
      <td data-th="var"><code>account</code></td>
      <td data-th="Value"><code>UA-XXXXX-Y</code></td>
      <td data-th="Defined By">Uzaktan yapılandırma</td>
    </tr>
    <tr>
      <td data-th="var"><code>clientId</code></td>
      <td data-th="Value">my user</td>
      <td data-th="Defined By">Tetikleme</td>
    </tr>
  </tbody>
</table>
