### Data

Poznámky k podobě xml šablony:
* `report/*/TopologicalConsistency/*/specification/*/title` -- chybí titulek v angličtině. Měl by se doplnit (a měl by ho doplnit pravděpodobně zákazník)

##### Výsledky validace CENIA (proti národnímu profilu ČR)
* (1.4)(b) -- Protokol není validní (`distributionInfo/*/transferOptions/*/onLine/*/linkage/*/protocol`)
  * https://iprpraha.cz/domains/iprpraha.cz/stranka/2606/digitalni-technicka-mapa-prahy
  * https://www.cuzk.cz/DMVS/Kontakty/DTM-kraju.aspx
  * v obou případech dává smysl pouze `https://services.cuzk.cz/registry/codelist/OnlineResourceProtocolValue/WWW:LINK-1.0-http--link`
* (5b) -- Časový rozsah chybí, nebo je nevalidní (`identificationInfo/*/extent/*/temporalElement`) -- upozornění, ignoruji

##### Výsledky validace INSPIRE
* md common-req C.22: Conformity Degree -- each Conformance Result includes degree of declared conformity using property with value true or false.
  * If the conformity of a dataset or service has not been evaluated, the according metadata record must contain a domain consistency element with a conformance result. The conformance result must have a 'pass' property that, if empty, indicates that conformity has not been evaluated yet. In this case, the 'pass' property element must have an XML attribute gco:nilReason='unknown'. This record has a conformance result with empty 'pass' property element but without such an XML attribute.
  * V šabloně metadat je pass element prázdný s atributem `gco:nilReason="missing"`. To je v rozporu s INSPIRE specifikací, která říká, že v případě, že test nebyl proveden, tam má být "unknown". Stejná metadata vygeneruje i národní metadatový profil.
* md datasets-and-series 1.4: INSPIRE Theme Keyword -- kontroluje, že téma, ke kterému data patří jsou identifikovaná prostřednictvím alespoň jednoho klíčového slova ze slovníku INSPIRE Spatial Data Themes. Je tam použit chybný překlad tématu US.
  * Asi není nutné naplnit, vzhledem k tomu, že není vyžadován soulad s INSPIRE a nejedná se o publikaci DS v souladu s INSPIRE specifikací.
  * V seznamu klíčových slov GEMET je odkaz na `https://inspire.ec.europa.eu/theme/us`, ale s hodnotou, která má být v topicCategory. V klíčových slovech má být Veřejné služby a služby veřejné správy.
* md lsdss 2.6: Data encoding -- Evaluate encoding and the storage or transmission format for data sets and series.
  * XML document 'dtm-psk-metadata-datova-sada-20230503.xml', record 'CZ-70883858-DTMP': The distributionFormat must have an attribute `gmd:version/@gco:nilReason` as 'unknown' or 'inapplicable', if version is empty.
  * V elementu gmd:version je gco:nilReason="missing". Podle INSPIRE specifikace je možné, aby nebyl vyplněn, ale musí mít nilReason "unknown" nebo "inapplicable". Tady doopravdy nevidím důvod, proč mít missing, když se dá použít jedno ze zmniňovaného. Ani SHP ani DGN verzování nepoužívají, proto navrhuji "inapplicable" (dokoknce je to tak popsáno v docx dokumentu).
* md lsdss 2.8: Topological consistency Descriptive results -- Test that the topological consistency descriptive results is provided correctly.
  * The conformance result must have a 'title' property. This value shall have the value "INSPIRE Data Specifications - Base Models - Generic Network Model" regardless of the metadata language.
  * INSPIRE validace vyžaduje INSPIRE test na kontrolu topologie. Ten zákazník pravděpodobně nevyžaduje, tuto chybu je tedy možné ignorovat. Pro úplnou validitu by bylo možné přidat i INSPIRE test a element pass s hodnotou false. Validátor neočekává jiný topology consistency test než INSPIRE, takže stejně háže chybu

##### Opětovná CENIA validace po opravě INSPIRE chyb

* chyba (1.4)(b) je opravená, žádné jiné se neobjevily

##### Poznámky k docx
* jako geografický lokátor je použita "Praha", ale s gmx:Anchor linkem na http://publications.europa.eu/resource/authority/country/CZE, což neodpovídá. Podle profilu pro Prahu odpovídá: `<gmx:Anchor xlink:href="https://linked.cuzk.cz/resource/ruian/vusc/19">Hlavní město Praha</gmx:Anchor>`. Nahrazeno v MD
* název CRS: "S-JTSK 3. kvadrant" není oficiální název, to je "S-JTSK / Krovak East North". Doporučuji změnu v XML.
* Podle docx je položka CZ-13 Garant a zároveň Gestor. To ale podle četnosti prvků není možné, musí být pouze jeden. Vyplnil jsem tam Garanta, který je v docxu uveden první, tedy `<gmx:Anchor xlink:href="https://services.cuzk.cz/registry/codelist/ObligatoryProviderRoleValue/Guarantee">Garant</gmx:Anchor>`. V docx je navíc špatně uveden anglický ekvivalent (Guarantor místo guarantee). To by bylo dobré přepsat v docxu (přidal jsem tam comment). Z definice v profilu: `Položka může nabývat hodnot:
„gestor” – gestor národní datové sady INSPIRE,
„spolugestor” – spolugestor národní datové sady INSPIRE,
„garant” – garant národní datové sady INSPIRE.
Položka se vztahuje k organizaci, která má v položce 9.1
Odpovědná osoba nebo organizace (Responsible party)
uvedenou roli „Správce“ (Custodian).`
CUstodian má v tabulce garanta, další důvod pro zvolení garanta.

##### Seznam změn z diffu

* `identificationInfo/*/citation/*/otherCitationDetails` - role převedena z Gestora na Garanta. V docx jsou uvedeny obě, ale garant je uveden výše a NENÍ možné mít obě.
* `identificationInfo/*/descriptiveKeywords/*/keyword` - INSPIRE klíčové slovo pro téma US je česky "Veřejné služby a služby veřejné správy" a anglicky "Utility and governmental services".
* `identificationInfo/*/extent/*/geographicElement/*/geographicIdentifier` - nahradil jsem stávající anchor
      <gmx:Anchor xlink:href="http://publications.europa.eu/resource/authority/country/CZE">Praha</gmx:Anchor>
tím, který je uveden v národním MD profilu, tedy:
      <gmx:Anchor xlink:href="https://linked.cuzk.cz/resource/ruian/vusc/19">Hlavní město Praha</gmx:Anchor>
* `distributionInfo/*/distributionFormat/*/version` pro SHP a DGN změněna na
      <gmd:version gco:nilReason="inapplicable"/>
* `distributionInfo/*/transferOptions/*/protocol` v obou případech vyplněn
      <gmd:protocol>
        <gmx:Anchor xlink:href="https://services.cuzk.cz/registry/codelist/OnlineResourceProtocolValue/WWW:LINK-1.0-http--link"/>
      </gmd:protocol>`
* `dataQualityInfo/*/report/*/result/*/specification/*/title` přidal jsem xsi:type="gmx:Anchor_Type", ale asi není nutné,
* `dataQualityInfo/*/report/*/result/*/pass` podle INSPIRE Specifikace je neprovedený test uveden prázdným elementem pass s nilReason unknown (byl missing, který neprochází INSPIRE validací)
`<gmd:pass gco:nilReason="unknown"/>`
ve staré verzi byl nilReason="missing"
* přidal jsem ještě jeden TopologicalConsistency report se specifikací odpovídající INSPIRE s hodnotou `<pass>false</pass>`. Může se smazat, ale říká to, že data neodpovídají specifikaci INSPIRE.

### Služby

U služeb vycházím z šablony pro data. Některé chyby, které se vyskytly v šabloně dat se vyskytly i v šabloně pro služby a jsou opraveny. Jedná se o:
* `gco:nilReason="missing"` namísto "inapplicable" nebo "unknown" pro verzi a výsledek testu doménové konzistence,
* změna geografického identifikátoru na hlavní město Praha,
* změna role povinného poskytovatele z gestora na garanta.

Jiné se naopak ve službách vůbec nevyskytují, jedná se o klíčové slovo INSPIRE a pravděpodobně výsledek testu Topologické konzistence.

Protokol pro službu WMS je jiný. Doplnil jsem

      <gmd:protocol>
        <gmx:Anchor xlink:href="https://services.cuzk.cz/registry/codelist/OnlineResourceProtocolValue/OGC:WMS">OGC:WMS</gmx:Anchor>
      </gmd:protocol>
, ale ve finále by to tam mělo být i s verzí, tedy

      <gmd:protocol>
        <gmx:Anchor xlink:href="https://services.cuzk.cz/registry/codelist/OnlineResourceProtocolValue/OGC:WMS-1.3.0-http-get-capabilities">OGC:WMS-1.3.0-http-get-capabilities</gmx:Anchor>
      </gmd:protocol>
, pokud verze bude 1.3.

Tyto úpravy byly provedeny ještě před ostatními testy a validací.

##### Výsledky validace CENIA (proti národnímu profilu ČR)

* (1.4)(a) na stránce https://iprpraha.cz/domains/iprpraha.cz/stranka/2606/digitalni-technicka-mapa-prahy/wms nic není, předpokládám, že to bude doplněno později, ovšem podle národního profilu tady musí být odkaz na dokument getCapabilities.
* (1.4) (c) typ dokumentu eodpovídá protokolu -- souvisí s předchozím, tedy s tím, že tam nic není. Validátor očekává dokument getCapabilites.

* (1.6) warning, chybí vázaný zdroj. element operatesOn by měl odkazovat na metadata dat, nad kterými je služba provozována, tedy nad MD datové sady. V šabloně je uveden kus kódu z komentářem 1.6, ale tímto způsobem se vázaný zdroj neuvádí. Správně patří do elementu operatesOn na konci identificationInfo panelu. Do šablony byl přidán element
      <srv:operatesOn xlink:href="link.na.md" xlink:title="CZ-70883858-DTMP" xlink:type="simple"/>
Po opětovné validaci vrací chybu, že na zdroji link.na.md nic není, což není překvapivé. Je potřeba nahradit ho odkazem na MD dat.
Rozpracovaný text pro element `<coupledResource>` jsem smazal.

Jiné chyby ve validaci nejsou.

##### Výsledky validace INSPIRE

* md sds 3.1: Resource Type -- Test that a resource type is provided as service.
  * Only one gmd:hierarchyLevelName element shall be provided. 0 elements have been provided.
  * doplněn element `<gmd:hierarchyLevelName>
    <gco:CharacterString>služba</gco:CharacterString>
  </gmd:hierarchyLevelName>`.
* md sds 3.6: Coupled Resource: -- Test that an operatesOn element refers to the target spatial data set(s) of the service, being implemented by reference.
  * hlásí, že na odkazu v operatesOn nic není. TO je pravda, až tam budou metadata dat, problém bude vyřešen.

* md sds 3.8: Only One Data Quality Element -- Test that a resource locator linking to the described Spatial Data Service is given if online access is available. If not, tests that is provided an URL pointing to an online resource providing additional information of the service, if available.
  * The MD_ScopeCode element must have a codeListValue attribute with value service.
  * způsobeno tím, že v dataQualityInfo v položce scope je dataset. Musí tam být service a musí být vyplněn description.
  * bylo doplněno:

        <gmd:scope>
           <gmd:DQ_Scope>
            <gmd:level>
              <gmd:MD_ScopeCode codeListValue="service" codeList="http://standards.iso.org/iso/19139/resources/gmxCodelists.xml#MD_ScopeCode"/>
            </gmd:level>
            <gmd:levelDescription>
              <gmd:MD_ScopeDescription>
                <gmd:other>
                  <gco:CharacterString>služba</gco:CharacterString>
                </gmd:other>
              </gmd:MD_ScopeDescription>
            </gmd:levelDescription>
          </gmd:DQ_Scope>
        </gmd:scope>

Při opětovné validaci zůstal pouze problém md sds 3.6, který bude vyřešen tím, že `operatesOn` bude odkazovat skutečný odkaz na metadata vázaného zdroje.

##### Poznámky k docx

Platí stejné poznámky jako pro data. Zejména chybějící protokol u verze, nemožnost zdvojení prvku CZ - 13 a nesprávný název S-JTSK 3. kvadrant.

##### Seznam změn z diffu
* `hierarchyLevelName` je přidán celý nový s hodnotou služba: `<gmd:hierarchyLevelName>
        <gco:CharacterString>služba</gco:CharacterString>
    </gmd:hierarchyLevelName>`
* `identificationInfo/*/citation/*/otherCitationDetails` - role převedena z Gestora na Garanta. V docx jsou uvedeny obě, ale garant je uveden výše a NENÍ možné mít obě.
* `identificationInfo/*/extent/*/geographicElement/*/geographicIdentifier` - nahradil jsem stávající anchor
      <gmx:Anchor xlink:href="http://publications.europa.eu/resource/authority/country/CZE">Praha</gmx:Anchor>
tím, který je uveden v národním MD profilu, tedy:
      <gmx:Anchor xlink:href="https://linked.cuzk.cz/resource/ruian/vusc/19">Hlavní město Praha</gmx:Anchor>
* `identificationInfo/*/operatesOn` je nově přidaný element, který obsahuje odkaz na metadata datové sady. Namísto toho byl odstraněn celý element `identificationInfo/*/coupledResource`.
* `distributionInfo/*/distributionFormat/*/version` pro image/png změněna na
      <gmd:version gco:nilReason="inapplicable"/>
* `distributionInfo/*/transferOptions/*/protocol` vyplněn
      `<gmd:protocol>
        <gmx:Anchor xlink:href="https://services.cuzk.cz/registry/codelist/OnlineResourceProtocolValue/OGC:WMS">OGC:WMS</gmx:Anchor>
      </gmd:protocol>`
      pro WMS a
      `<gmd:protocol>
         <gmx:Anchor xlink:href="https://services.cuzk.cz/registry/codelist/OnlineResourceProtocolValue/WWW:LINK-1.0-http--link"/>
       </gmd:protocol>`
       pro pseudoprohlížečku. Tady si navíc nejsem jistý, jestli by v MD pro WMS vůbec měl být odkaz sem.
* `dataQualityInfo/*/scope` obsahuje odkaz do číselníku s hodnotou service a nenulový string jako jeho popis:
           <gmd:scope>
                <gmd:DQ_Scope>
                    <gmd:level>
                        <gmd:MD_ScopeCode codeListValue="service"
                            codeList="http://standards.iso.org/iso/19139/resources/gmxCodelists.xml#MD_ScopeCode"
                        />
                    </gmd:level>
                    <gmd:levelDescription>
                        <gmd:MD_ScopeDescription>
                            <gmd:other>
                                <gco:CharacterString>služba</gco:CharacterString>
                            </gmd:other>
                        </gmd:MD_ScopeDescription>
                    </gmd:levelDescription>
                </gmd:DQ_Scope>
            </gmd:scope>
* `dataQualityInfo/*/report/*/result/*/pass` podle INSPIRE Specifikace je neprovedený test uveden prázdným elementem pass s nilReason unknown (byl missing, který neprochází INSPIRE validací)
`<gmd:pass gco:nilReason="unknown"/>`
ve staré verzi byl nilReason="missing"
