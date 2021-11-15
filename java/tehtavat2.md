---
layout: page
title: Viikko 2
inheader: no
permalink: /java/tehtavat2/
---

{% include miniproj_ilmo.md %} 

{% include laskari_info.md part=2 %}

Viikon ensimmäisessä tehtävässä tutustutaan tarkemmin gradleen. Toinen ja kolmas tehtävä käsittelevät koodin _staattisen analyysin_ työkalua checkstyleä. Gitiin tutustuminen jatkuu tehtävissä 4-8. Laskarien lopuksi jatketaan _riippuvuuksien injektoinnin_ parissa. 

### Typoja tai epäselvyyksiä tehtävissä?

{% include typo_instructions.md path="/tehtavat2.md" %}

### Tehtävien palauttaminen

Osa git-tehtävistä (tehtävät 4-6) tehdään ainoastaan paikalliseen repositorioon, eli ne eivät näy palautuksessa mitenkään.

Muut tehtävät palautetaan GitHubiin, sekä merkitsemällä tehdyt tehtävät palautussovellukseen <{{site.stats_url}}>

Tehtävät 2 ja 3 laajentavat viime viikon ensimmäistä tehtäväsarjaa, eli ne palautetaan samaan repositorioon kuin Ohtuvarasto. Muut tehtävät voit palauttaa samaan repositorioon mihin palautit ensimmäisen viikon tehtävät 14-16.

Katso tarkempi ohje palautusrepositorioita koskien [täältä](/tehtavat1#teht%C3%A4vien-palautusrepositoriot).

### Huomio gradleen liittyen

Käytämme tälläkin viikolla gradle-muotoisia projekteja. Jos gradle-koodi lukee syötteitä komentoriviltä, tulee määrittelytiedostojen loppuun liittää seuraava

```groovy
run {
    standardInput = System.in
}
```

Ilman tätä määrittelyä ohjelmaa gradlella suorittaessa, eli komennolla `gradle run`, ohjelma ei pääse käsiksi syötevirtaan ja scannerin luominen epäonnistuu.

Tämän viikon tehtäviin liittyviin projekteihin määrittely on jo lisätty.

Jos ohjelma lukee syötteitä käyttäjältä, kannattaa se suorittaa komennolla `gradle -q --console plain run`, jolloin gradlen tekemät tulostukset eivät tule konsoliin.

*HUOM!* näyttää siltä, että NetBeans 11.1:llä Scanner ei toimi ollenkaan gradle-projekteissa, eli jos törmäät samaan ongelmaan, suorita ohjelmat komentoriviltä.

### 1. gradlen perusteita

Olemme jo käyttäneet gradlea hyvällä menestyksellä viikon ajan. Tutustutaan nyt gradleen hieman tarkemmin tekemällä [täällä](/gradle) oleva interaktiivinen "tutoriaali".

### 2. lisää gradlea: koodin staattinen analyysi

Kurssin [kolmannessa osassa](/osa3) teemana on ohjelmien laadun varmistaminen. Eräs ohjelman laatua useimmiten edistävä tekijä on järkevän _koodityylin_ noudattaminen.  Koodin tyyliä voidaan tarkkailla automatisoidusti ns. staattisen analyysin työkaluilla.

Tutustutaan nyt staattisen analyysin työkaluun [checkstyleen](https://checkstyle.sourceforge.net/), jonka käyttäminen on suhteellisen vaivatonta gradlen [checkstyle-pluginin](https://docs.gradle.org/current/userguide/checkstyle_plugin.html) avulla.

Mene nyt viikon 1 tehtävien _Ohtuvarastoon_ liittyvien tehtävien palautusrepositorioosi.

Lisää projektiin checkstyle-plugin [tämän ohjeen mukaan](https://docs.gradle.org/current/userguide/checkstyle_plugin.html), eli muuttamalla konfiguraationtiedoston _build.gradle_ alkuosaa seuraavasti:

```
plugins {
    id 'application'
    id 'jacoco'
    id 'checkstyle'
}
```

Suorita komento `gradle checkstyleMain`

Suoritus epäonnistuu, virheilmoitus kertoo mistä kyse:

<pre>
* What went wrong:
Execution failed for task ':checkstyleMain'.
> Unable to create Root Module: config {/Users/mluukkai/dev/ohtu-poc/config/checkstyle/checkstyle.xml}, classpath {/Users/mluukkai/dev/ohtu-poc/build/classes/java/main:/Users/mluukkai/dev/ohtu-poc/build/resources/main:/Users/mluukkai/.gradle/caches/modules-2/files-2.1/com.google.guava/guava/29.
</pre>

Eli kuten [manuaali kertoo](https://docs.gradle.org/current/userguide/checkstyle_plugin.html#sec:checkstyle_project_layout), Gradle olettaa että projektista löytyy checkstylen toiminnan määrittelevä konfiguraatiotiedosto _checkstyle.xml_ joka tulee sijoittaa projektin juuren alle tehtävään hakemistoon _config/checkstyle_.

Luo tiedosto, ja hae tiedostolle sisältö [täältä]({{site.java_exercise_repo_url}}/blob/main/koodi/viikko2/checkstyle.xml).


Huomaa, että tiedoston tulee olla oikeassa paikassa. Virheilmoitus ja [manuaali](https://docs.gradle.org/current/userguide/checkstyle_plugin.html#sec:checkstyle_project_layout) kertovat oikean sijainnin!

Kun nyt suoritat komennon `gradle checkstyleMain`, tulee jälleen virhe, mutta nyt virheen syynä on se, että koodi rikkoo konfiguraatiotiedostossa määriteltyjä tyylisääntöjä. Virheilmoitus kertoo raportin sijainnin:

<pre>
* What went wrong:
Execution failed for task ':checkstyleMain'.
> Checkstyle rule violations were found. See the report at: file:///Users/mluukkai/opetus/ohtu2020/ohtu-2021-viikko1/build/reports/checkstyle/main.html
</pre>

Avaa raportti selaimella. Huomaat, että tuloksena on suuri määrä virheitä. Valitettavasti virheraportti kertoo ainoastaan sen koodirivin, mistä virhe löytyy. Joudut katsomaan vastaavan kohdan koodistasi esim. NetBeansista.

**Toimi nyt seuraavasti**

* Poista checkstylen konfiguraatiotiedostosta kaikki määritellyt säännöt siten, että sen sisällöksi jää vain

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE module PUBLIC "-//Puppy Crawl//DTD Check Configuration 1.3//EN" "http://www.puppycrawl.com/dtds/configuration_1_3.dtd">
<module name="Checker">

</module>
```

* Suorita `gradle checkstyleMain` ja varmista, että tarkastus menee läpi
* Määrittele nyt tiedostoon seuraavat säännöt (ks. kohta checks checkstylen [sivuilta](https://checkstyle.sourceforge.io/checks.html)):
  * metodien pituus max 15 riviä (tämä ja seuraavat säännöt määritellään moduulin tree walker sisälle)
  * ei yli yhtä sisäkkäisiä if-komentoja  
  * ei sisäkkäisiä for-komentoja, seuraavan siis pitäisi aiheuttaa virhe:
  ```java
    for( int i=0; i<1; i++ ) {
      for( int j=0; i<j; j++ ) {
        System.out.println("virhe");
      } 
    }
  ```
  * koodi on oikein sisennettyä
  * lohkon avaava aaltosulku on aina rivin lopussa, eli esim. ehtolauseissa aaltosulku tulee merkitä
    ```java
    if ( ehto ) 
    {
      System.out.println("virhe");
    }
    ```
  sijaan seuraavasti:
    ```java
    if ( ehto ) {
      System.out.println("virhe");
    }
    ```  
  * syklomaattinen koodikompleksisuus korkeintaan 3 (selvitä mitä tarkoittaa!)

* Muuta koodiasi siten, että saat jokaisen määritellyistä checkstyle-säännöistä rikkoutumaan
* Korjaa koodisi ja varmista, että se noudattaa kaikkia sääntöjä
  * pääohjelman koodin voi poistaa tarvittaessa kokonaan, jotta saat koodin säännönmukaiseksi  

### 3. Koodin staattinen analyysi ja GitHub Actionit 

Laajenna projektisi GitHub Actionien määritelmää siten, että myös checkstyle-tarkastukset suoritetaan aina kun koodi pushataan GitHubiin.

Varmista, että GitHub huomaa tilanteen, missä koodi rikkoo projektin checkstyle-sääntöjä:

![]({{ "/images/lh2-11.png" | absolute_url }})

Varmista myös, että kun korjaat koodin, kaikki toimii taas moitteettomasti:

![]({{ "/images/lh2-12.png" | absolute_url }})

### 4. git: branchit [versionhallinta]

lue brancheja käsittelevät osuudet seuraavasta <https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging>
* jos haluat lukea hieman perusteellisemman selityksen asiasta, lue <https://git-scm.com/book/en/v2:n luku kolme kokonaisuudessaan

Kannattaa huomioida myös erittäin hyvä brancheja käsittelevä visuaalinen materiaali osoitteessa <https://learngitbranching.js.org/>

Varsin selkeältä vaikuttaa myös <https://www.atlassian.com/git/tutorials/using-branches>

**huom:** kun liikut branchien välillä kannattaa pitää working tree ja staging -alue tyhjinä!

tee seuraavat paikalliseen git-repositorioosi (kyseessä ei siis tarvitse olla tehtävien palautusrepositorio)

* luo repositorio ja committaa mainiin tiedosto __main1.txt__
* luo branch __eka__, siirry branchiin, luo sinne tiedosto __eka.txt__ ja committaa
* siirry takaisin __main__-branchiin, tiedoston __eka.txt__ ei pitäisi nyt näkyä
  * **huom:** muistutus vielä siitä, että kun siirryt branchista toiseen varmista **aina** komennolla _git status_ että kaikki muutokset on committoitu 
* lisää ja committaa __mainiin__ tiedosto __maini2.txt__
* mene branchiin __eka__ ja tarkasta, että __mainiin__ lisätty tiedosto ei ole branchissa
* lisää branchiin tavaraa, esim. tiedosto __eka2.txt__ ja committaa
* siirry takaisin __main__-branchiin
* tarkasta että __eka__-branchiin lisätyt muutokset eivät ole mainissa
* tarkastele komennolla <code>gitk --all</code> miltä repositorio ja branchit näyttävät (gitk toimii windowsilla ainakin Github for Windowsin Git Shellissä.)
  * gitk ei toimi maceissa, hyvä korvaaja sille on [sourcetree](https://www.sourcetreeapp.com)
* mergeä branchin __eka__ sisältö __mainiin__
* katso jälleen miltä näyttää gitk --all

### 5. git: branchit ja staging-alue [versionhallinta]

* olet nyt repositoriosi main-haarassa
* luo uusi tiedosto _uusi_tiedosto.txt_, **älä** kuitenkaan lisää ja commitoi tiedostoa
* komennon _git status_ tulostuksen pitäisi olla seuraava

```
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	uusi_tiedosto.txt

nothing added to commit but untracked files present (use "git add" to track)
```

* siirry nyt branchiin __eka__
* suorita uudelleen komento _git status_
* huomaat, että tulostus on edelleen sama, tiedosto ei edelleenkään ole versionhallinnan alla
* eli vaikka olit main-haarassa kun loit tiedoston, ei main-haara eikä koko git tiedä tiedostosta vielä mitään ennen kuin lisäät sen versionhallinnan alaisuuteen komennolla _git add_
* lisää tiedosto nyt versionhallinnan alaisuuteen ja commitoi se
* tiedosto menee nykyiseen branchiisi, eli branchiin _eka_, main ei edelleenkään tiedä tiedostosta mitään
* luo uusi tiedosto _uusi_tiedosto2.txt_ ja lisää se versionhallintaan, älä kuitenkaan commitoi
* tarkasta että komennon _git status_ tulos on

```
On branch eka
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   uusi_tiedosto2.txt
```

* olet siis branchissa _eka_ ja _uusi_tiedosto2.txt_ on lisätty staging-alueelle, sitä ei kuitenkaan ole vielä committoitu
* siirry nyt branchiin __main__ 
* komennon _git status_ tulos on edelleen sama, _uusi_tiedosto2.txt_ on edelleen staging-alueella mutta committoimattomana
* staging-alue __ei kuulu__ mihinkään branchiin, eli jos staging-alueella on committoimattomia muutoksia ja vaihdat branchia, säilyvät samat asiat stagingissa
* muutokset siirtyvät stagingista branchiin ainoastaan komennolla _git commit_ 
* committoi nyt staging-alueen muutokset eli _uusi_tiedosto2.txt_ mainiin
* komennon  _git status_ tulos kertoo nyt että staging-alue on tyhjä:

```
On branch main
nothing to commit, working tree clean
```

* siirry jälleen branchiin __eka__ ja huomaat, että _uusi_tiedosto2.txt_ ei ole olemassa
* mergeä __main__ branchiin __eka__
* siirry nyt mainiin ja tuhoa branchi __eka__  
* tämän tehtävän ideana oli siis havainnollistaa, että working tree (muutokset joista git ei ole tietoinen) ja staging (gitiin lisättyihin tiedostoihin tehdyt committoimattomat muutokset)
**eivät liity** mihinkään branchiin, muutokset siirtyvät staging-alueelta branchiin ainoastaan komennon _git commit_ suorituksen seurauksena

### 6. git: konflikti! [versionhallinta]

Tee paikalliseen git-repoon seuraavat

* lisää __main__-branchiin tiedosto __tarkea.txt__, kirjota sinne muutama rivi tekstiä ja committaa
* tee uusi branchi __toka__, mene branchiin ja editoi tiedoston __tarkea.txt__ loppua (lisää esim loppuun muutama uusi rivi) ja committaa
* mene takaisin __main__-branchiin, editoi tiedoston __tarkea.txt__ alkua (lisää alkuun muutama rivi) ja committaa
* mergeä branchin __toka__ sisältö __mainiin__
  * mergeäminen aiheuttaa ns merge-commitin, ja avaa tekstieditorin mihin joudut kirjoittamaan commit-viestin
    * jos et ole määritellyt gitille editoria viime viikon [tehtävän 2](/tehtavat1/) ohjeiden mukaan, avautuu ehkä gitin oletusarvoinen editori [vim](http://www.vim.org)
    * vimistä poistuminen saattaa osoittautua ensikertalaiselle hankalaksi, google auttaa tarvittaessa
  * katso tiedoston __tarkea.txt__-sisältöä, sen pitäisi sisältää nyt molemmissa brancheissa tehdyt muutokset
  * **huom:** jo tässä vaiheessa saattaa syntyä konflikti jos olet vahingossa muuttanut merkkejä väärästä kohtaa tiedostoa! Toimi tällöin ao. ohjeen mukaan.
* lisää jotain tiedoston loppuun ja committaa
* siirry branchiin __toka__
* lisää jotain tiedoston __tarkea.txt__ loppuun ja committaa
* mergeä branchin __main__ sisältö branchiin __toka__
  * nyt pitäisi aiheutua konflikti, komento aiheuttaa tulostuksen
```
Auto-merging tarkea.txt
CONFLICT (content): Merge conflict in tarkea.txt
Automatic merge failed; fix conflicts and then commit the result.
```
* ratkaise konflikti:
  * editoi tiedoston __tarkea.txt__ sisältö haluamaksesi
  * ja toimi em. artikkelien ohjeen mukaan eli lisää konfliktoinut tiedosto staging-alueelle ja committoi

Jotkut editorit, esim [visual studio code](https://code.visualstudio.com) sisältävät sisäänrakennetusti ns. _merge toolin_, joka osaa jossain määrin helpottaa konfliktien ratkaisua:

![](https://github.com/mluukkai/ohjelmistotuotanto2017/raw/main/images/lh2-4a.png)

### 7. git: branchit ja GitHub [versionhallinta]

Aloita lukemalla ProGit kirjasta luku [Remote Branches](http://git-scm.com/book/en/Git-Branching-Remote-Branches)

Lisätään seuraavaksi branch githubiin:

* lisää tehtävien palauttamiseen käyttämäsi GitHub-repositorion paikalliseen kopioon branchit __haara1__ ja __haara2__
* mene branchiin __haara1__, lisää sinne tiedosto __haara1.txt__ ja committaa
* mene branchiin __haara2__, lisää sinne tiedosto __haara2.txt__ ja committaa
* pushaa uudet branchit GitHubiin
* tarkastele GitHub-repositoriota selaimella, varmista että branchit syntyvät ja niillä on haluttu sisältö

Kloonaa GitHub-repositoriosta koneellesi toinen kopio

* kuten huomaat, eivät branchit tule kloonattuun kopioon
* tee paikalliseen kopioon branch joka "träkkää" GitHub:issa olevan projektisi branchia __haara1__ (ks. <http://git-scm.com/book/en/Git-Branching-Remote-Branches> kohta Tracking Branches)
* lisää "träkkäävään" branchiin joku tiedosto, committaa ja pushaa branchi GitHubiin
* tarkastele GitHub-repositoriota selaimella, varmista että branchi päivittyy

Mene GitHub-repon alkuperäiseen paikalliseen kopioon

* mene branchiin __haara1__ ja pullaa muutokset GitHub:in vastaavasta branchista
  * huom: koska kyseessä ei ole "träkkäävä" branchi, joudut pullaamaan komennolla <code>git pull origin haara1</code>
* mene branchiin __haara2__, lisää sitten tiedosto, committaa ja pushaa branchi GitHubiin
  * koska kyseessä ei ole "träkkäävä" branchi, ei vanhemmilla gitin versiolla komento _git push_ riitä vaan joudut määrittelemään branchin jonne push kohdistuu eli antamaan komennon <code>git push origin haara2</code>
  * uudemmilla gitin versioilla pushaus onnistuu suoraan

Mene jälleen toiseen kopioon

* suorita komento <code>git remote show origin</code>
  * komento kertoo 'origin':issa eli githubissa olevien branchien ja paikallisten branchien suhteen 
* tee sinne GitHub:issa olevan projektisi branchia __haara2__ träkkäävä branch
* suorita jälleen <code>git remote show origin</code>, mitä muutoksia huomaat?
* tee branchiin muutoksia ja pushaa ne githubiin
  *  huom: koska kyseessä on träkkäävä branch, riittää git push
* tarkastele GitHub-repositoriota selaimella, varmista että branchi päivittyy

Palaa vielä alkuperäiseen lokaaliin repositorioon

* suorita komento <code>git remote show origin</code> 
* tulostus kertoo, että lokaaleista haaroista ainoastaan _main_ on konfiguroitu komennon _git pull_ osalta, eli on träkkäävä branchi:

```
* remote origin
  Fetch URL: git@github.com:mluukkai/ohtu-2021-viikko1.git
  Push  URL: git@github.com:mluukkai/ohtu-2021-viikko1.git
  HEAD branch: main
  Remote branches:
    haara1 tracked
    haara2 tracked
    main tracked
  Local branch configured for 'git pull':
    main merges with remote main
  Local refs configured for 'git push':
    haara1 pushes to haara1 (up to date)
    haara2 pushes to haara2 (up to date)
    main pushes to main (up to date)
```
* suorita _git pull_ branchissä _haara1_
* komennon tuloste antaa ohjeen, miten saat konfiguroitua _haara1_:n träkkäämään githubissa olevaa haaraa:

```
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> haara1
 
```

* Kun annat komennon, sen jälkeen haara träkkää githubissa olevaa haaraa ja komento _git pull_ voidaan antaa ilman parametreja

Branchien kanssa työskentely voi aluksi tuntua sekavalta varsinkin jos GitHub:issa on myös useita brancheja.

### Mihin brancheja käytetään?

Ohjelmistotiimi voi soveltaa Gitin branchaystä hyvin monella eri tyylillä. Artikkeli
<https://www.atlassian.com/git/tutorials/comparing-workflows> esittele tähän muutamia vaihtoehtoja. Eräs yleinen tapa branchien käyttöön ovat ns. _featurebranchit_:

> The core idea behind the Feature Branch Workflow is that all feature development should take place in a dedicated branch instead of the main branch. This encapsulation makes it easy for multiple developers to work on a particular feature without disturbing the main codebase. It also means the main branch will never contain broken code, which is a huge advantage for continuous integration environments.

Jos kiinnostaa, lue lisää yo. dokumentista.

### 8. git: epäajantasaisen kopion pushaaminen [versionhallinta]

Demonstroidaan usein esiintyvää tilannetta, jossa epäajantasaisen repositorion pushaaminen githubissa olevaan etärepositorioon epäonnistuu.

* mene alkuperäisen repositorion paikallisen kopion __main__ -haaraan, tee joku muutos, commitoi ja pushaa se githubiin
* mene toisen kopion __main__-haaraan ja  tee sinne joku muutos 
* commitoi ja pushaa muutos githubiin
* kaikki ei kuitenkaan mene hyvin, seurauksena on seuraavantyylinen virheilmoitus:

<pre>
$ git push
To git@github.com:mluukkai/ohtu-viikko1-2020.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'git@github.com:mluukkai/ohtu-viikko1-2020.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first merge the remote changes (e.g.,
hint: 'git pull') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
$ 
</pre>

Virheen syynä on se, että githubissa oleva __main__-haara oli edellä paikallisen repositorion __main__-haaraa. Ongelma korjaantuu tekemällä ensin <code>git pull</code>, ratkaisemalla mahdolliset konfliktit ja pushaamalla sitten uudelleen
* komennon _git pull_ yhteydessä syntyy merge-commit, ja avautuu tekstieditori mihin joudut kirjoittamaan commit-viestin
* eli toimi näin ja varmista, että tekemäsi muutokset menevät githubiin

### 9. riippuvuuksien injektointi osa 3: Verkkokauppa

Tutustuimme viime viikon [tehtävissä 14-16](/tehtavat1#14-riippuvuuksien-injektointi-osa-1) riippuvuuksien injektointiin ja sovelsimme sitä yksikkötestauksen helpottamiseen.

Jos asia on päässyt unohtumaan, voit kerrata asian lukemalla [tämän](/riippuvuuksien_injektointi/).

Kurssirepositorion hakemistossa [koodi/viikko2/Verkkokauppa1]({{site.java_exercise_repo_url}}/tree/main/koodi/viikko2/Verkkokauppa1) on yksinkertaisen verkkokaupan ohjelmakoodi

* Hae projekti kurssirepositoriosta
  * järkevintä lienee että kloonaat kurssirepositorion paikalliselle koneellesi jos et ole sitä jo tehnyt, jos olet, niin pullaa repositorio ajantasalle
  * **tämän jälkeen kannattaa kopioida projekti tehtävien palautukseen käyttämäsi repositorion sisälle**
* Tutustu koodiin, piirrä luokkakaavio ohjelman rakenteesta
  * luokkakaavioita ei tarvitse palauttaa...
* Ohjelman luokista <code>Pankki</code>, <code>Varasto</code>, <code>Viitegeneraattori</code> ja <code>Kirjanpito</code> ovat sellaisia, että niistä on tarkoitus olla olemassa ainoastaan yksi olio. Tälläisiä ainutkertaisia olioita sanotaan **singletoneiksi**. Koodissa singletonit ovat toteutettu "klassisella tavalla", eli piilottamalla konstruktori ja käyttämällä staattista muuttujaa ja metodia säilömään ja palauttamaan luokan ainoa olio
  * Singleton on ns. [GoF-kirjan](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612) yksi alkuperäisistä suunnittelumalleista, lue lisää singletoneista esim. [täältä](https://sourcemaking.com/design_patterns/singleton)
  * Singleton ei ole erinäisistä syistä enää oikein muodissa, ja korvaamme sen seuraavassa tehtävässä

* Kuten huomaamme, on koodissa toivottoman paljon konkreettisia riippuvuuksia:
  * Varasto --> Kirjanpito
  * Pankki --> Kirjanpito
  * Kauppa --> Pankki
  * Kauppa --> Viitegeneraatori
  * Kauppa --> Varasto
* **Poista luokan <code>Kauppa</code> konkreettiset riippuvuudet** yllä mainittuihin luokkiin _rajapintojen avulla_
  * riippuvuus luokkaan Ostoskori voi jäädä, sillä se on ainoastaan luokan Kauppa sisäisesti käyttämä luokka ja täten varsin harmiton
  * *HUOM!:* NetBeansissa on automaattinen refaktorointiominaisuus, jonka avulla luokasta saa helposti generoitua rajapinnan, jolla on samat metodit kuin luokalla. Klikkaa luokan kohdalla hiiren oikeaa nappia, valitse refactor ja "extract interface"
  * muut riippuvuudet jätetään vielä
   
* **Määrittele luokalle <code>Kauppa</code> sopiva konstruktori**, jotta voit injektoida riippuvuudet, konstruktorin parametrien tulee olla tyypiltään _rajapintoja_
* Älä käytä luokan _Kauppa_ sisällä enää konkreettisia luokkia  _Varasto_, _Viitegeneraattori_ ja _Pankki_ vaan ainoastaan niitä vastaavia rajapintoja!
* **Muokkaa pääohjelmasi**, siten että se luo kaupan seuraavasti:

``` java
Kauppa kauppa = new Kauppa(
  Varasto.getInstance(), 
  Pankki.getInstance(), 
  Viitegeneraattori.getInstance() 
);
```

* Varmista ohjelman toimivuus suorittamalla se komentoriviltä komennolla _gradle run_

### 10. riippuvuuksien injektointi osa 4: ei enää singletoneja verkkokaupassa

* Singleton-suunnittelumallia pidetään [osittain ongelmallisena](http://rcardin.github.io/design/programming/2015/07/03/the-good-the-bad-and-the-singleton.html), poistammekin edellisestä tehtävästä singletonit
* **Poista** kaikista luokista <code>getInstance</code>-metodit ja staattinen <code>instance</code>-muuttuja
  * joudut muuttamaan luokilla olevat private-konstruktorit julkisiksi
* **Poista** rajapintojen ja riippuvuuksien injektoinnin avulla edellisen tehtävän jäljiltä jääneet riippuvuudet, eli
  * Varasto --> Kirjanpito
  * Pankki --> Kirjanpito
* **Muokkaa pääohjelmasi** vastaamaan uutta tilannetta, eli suunnilleen muotoon:

``` java
Viitegeneraattori viitegen = new Viitegeneraattori();
Kirjanpito kirjanpito      = new Kirjanpito();
Varasto varasto            = new Varasto(kirjanpito);
Pankki pankki              = new Pankki(kirjanpito);
Kauppa kauppa              = new Kauppa(varasto, pankki, viitegen);
```

Kuten huomaamme, alkaa kaupan konfigurointi olla aika vaivalloista...

### 11 Spring osa 1: riippuvuuksien injektointi

Kurssilla [Web-palvelinohjelmointi](https://courses.helsinki.fi/fi/tkt21007) käytettävä [Spring](https://spring.io/)-sovelluskehys tarjoaa pelastuksen käsillä olevaan tilanteeseen.

Lue nyt [täällä oleva](/riippuvuuksien_injektointi_spring/) kuvaus miten riippuvuuksien injektointi voidaan automatisoida Springillä

> Tulet todennäköisesti saamaan Springiä käyttäessäsi pitkiä ja kryptiseltä vaikuttavia virheilmoituksia. Lue virheilmoitusten _stack trace_ huolellisesti läpi, yleensä se antaa vihjeitä siitä, missä vika on. Virheilmoitusten tulkitseminen ja virheiden etsiminen on yksi tärkeimpiä taitoja ohjelmistoalalla, se voi tuntua ikävältä, mutta oikoteitä ei ole. Usein googlailu ja stack overflow auttavat, mutta kaikesta ei selviä pelkällä trial and error -menetelmällä. Usein käytettävän kirjaston toimintaa on ymmärrettävä jollain tasolla, jotta virheiden jäljitys onnistuu.
>
> Itse käytän virheiden jäljityksessä ns. paranoidimoodia. Jos olen epävarma siitä mitä teen (liittyen koodiin tai konfigurointiin), testaan lähes jokaisen rivin jälkeen, että toiminnallisuus on halutun kaltainen. Varmin keino aiheuttaa paljon kryptisiä virheitä on se, että testaa koodia mahdollisimman harvoin. Tällöin virheiden jäljitys on vaikeaa, sillä lisättyjä rivejä saattaa olla paljon ja virheen aiheuttaja ei välttämättä ole ilmeinen. Tälläisissä tilanteissa kannattaa esim. kommentoida lisätty koodi pois ja palauttaa lisäykset rivi kerrallaan.

### 12 Spring osa 2: Verkkokauppa siistiksi

Palataan sitten verkkokaupan pariin.

* Projektiin on konfiguroitu valmiiksi springin tarvitsemat riippuvuudet, sekä konfiguraatiot 
  * *HUOM!* mahdolliset virheilmoitukset __"org.springframework... package does not exist"__ katoavat kun buildaat projektin ensimmäisen kerran!
* Ota riippuvuuksien injektointi käyttöön lisäämällä luokille annotaatioita <code>@Component</code> ja <code>@Autowired</code> 
* Aloita muuttamalla pääohjelma siten, että ainoastaan viitegeneraattori luodaan Springin avulla. Muutos on suunnilleen seuraava:

``` java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
 
    Viitegeneraattori viitegen = ctx.getBean(Viitegeneraattori.class);
    Kirjanpito kirjanpito      = new Kirjanpito();
    Varasto varasto            = new Varasto(kirjanpito);
    Pankki pankki              = new Pankki(kirjanpito);
    Kauppa kauppa              = new Kauppa(varasto, pankki, viitegen);

    //...
}
```

* etene seuraavaan luokkaan:

``` java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
 
    Viitegeneraattori viitegen = ctx.getBean(Viitegeneraattori.class);
    Kirjanpito kirjanpito      = ctx.getBean(Kirjanpito.class);
    Varasto varasto            = new Varasto(kirjanpito);
    Pankki pankki              = new Pankki(kirjanpito);
    Kauppa kauppa              = new Kauppa(varasto, pankki, viitegen);

    //...
}
```

* ja seuraavaan:

``` java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
 
    Viitegeneraattori viitegen = ctx.getBean(Viitegeneraattori.class);
    Kirjanpito kirjanpito      = ctx.getBean(Kirjanpito.class);
    Varasto varasto            = ctx.getBean(Varasto.class);
    Pankki pankki              = new Pankki(kirjanpito);
    Kauppa kauppa              = new Kauppa(varasto, pankki, viitegen);

    //...
}
```

* Muuta sitten loputkin luokat käyttämään Springin riippuvuuksien injektointia, jolloin pääohjelman alku muuttuu muotoon: 

``` java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
 
    Kauppa kauppa = ctx.getBean(Kauppa.class);
    // ...
}
```

* Huom: pääohjelma tarvitsee kaupan lisäksi kirjanpito-olioa lopun tulostuksessa, sen saa haltuunsa Springin kontekstilta metodikutsulla _ctx.getBean(Kirjanpito.class)_

{% include submission_instructions.md %}
