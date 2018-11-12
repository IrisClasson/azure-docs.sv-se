>[!NOTE]
>För resurser som inte är fasta kan du be om att öka kvoterna genom att skapa ett supportärende. Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.

| Resurs | Standardgräns | 
| --- | --- | 
| Azure Media Services-konton (AMS) i en enskild prenumeration | 25 (fast) |
| Mediereserverade enheter per AMS-konto |25 (S1)<br/>10 (S2, S3) <sup>(1)</sup> | 
| Jobb per AMS-konto | 50,000<sup>(2)</sup> |
| Länkade uppgifter per jobb | 30 (fast) |
| Tillgångar per AMS-konto | 1,000,000|
| Tillgångar per uppgift | 50 |
| Tillgångar per jobb | 100 |
| Unik positionerare som är associerad med en tillgång vid ett tillfälle | 5<sup>(4)</sup> |
| Livekanaler per AMS-konto |5|
| Program i stoppat tillstånd per kanal |50|
| Program i körningstillstånd per kanal |3|
| Strömmande slutpunkter i körningstillstånd per AMS-konto|2|
| Strömningsenheter per slutpunkt för direktuppspelning |10 |
| Lagringskonton | 1 000<sup>(5)</sup> (fast) |
| Principer | 1 000 000<sup>(6)</sup> |
| Filstorlek| I vissa situationer kan finns det en gräns för maximal filstorlek för bearbetning i Media Services. <sup>7</sup> |
  
<sup>1</sup> ändrar du typ (till exempel från S2 till S1), den högsta gränsvärden som RU återställs.

<sup>2</sup> Det här värdet innefattar jobb i kö och avslutade, aktiva och avbrutna jobb. Det innefattar inte borttagna jobb. Du kan ta bort gamla jobb med **IJob.Delete** eller HTTP-begäran **DELETE**.

Från och med 1 April 2017 kommer tas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt bort, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Om du behöver arkivera jobb/uppgiftsinformationen kan du använda koden som beskrivs [här](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> när du gör en begäran till jobblistan entiteter kan högst 1 000 jobben returneras per begäran. Om du behöver följa upp alla skickade jobb kan du använda top/skip (maximalt antal poster som ska returneras/hoppa över) på det sätt som beskrivs i [OData system query options](https://msdn.microsoft.com/library/gg309461.aspx) (OData-systemfrågealternativ).

<sup>4</sup> Positionerare är inte utformade för att hantera åtkomstkontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management). Mer information finns i [det här](../articles/media-services/previous/media-services-content-protection-overview.md) avsnittet.

<sup>5</sup> Lagringskontona måste tillhöra samma Azure-prenumeration.

<sup>6</sup> Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Du bör använda samma princip-ID om du alltid använder samma dagar, åtkomstbehörigheter etc. Mer information och ett exempel finns i [det här](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies) avsnittet.

<sup>7</sup>om du ska överföra innehåll till en tillgång i Azure Media Services för att bearbeta den med en av medieprocessorerna i tjänsten (d.v.s. kodare som Media Encoder Standard och Media Encoder Premium Workflow eller analysmotorer som Face Detector), sedan ska du vara medveten om begränsningarna för maximal filstorlek som stöds. 

Den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare begränsningar gäller dock i Azure Media Services baserat på de storlekar som används av tjänsten. I följande tabell visas begränsningarna på var och en av de Mediereserverade enheter (S1, S2, S3) Om källfilen är större än de gränser som definierats i tabellen, inte dina kodningsjobb. Om du kodar 4K upplösning källor för lång tid behöver du använda Mediereserverade S3-enheter för att uppnå prestanda som behövs. Om du har 4K-innehåll som är större än gränsen 260 GB den Mediereserverade S3-enheter kan du kontakta oss på amshelp@microsoft.com för möjliga lösningar för ditt scenario.

| Mediereserverad enhet | Maximal inkommande storlek (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
