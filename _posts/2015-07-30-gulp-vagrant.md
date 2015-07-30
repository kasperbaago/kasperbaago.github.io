---
title: Brug Vagrant med Gulp
layout: post

---
Hos [Edentic](http://edentic.dk) arbejder vi med at lave vores løsninger med slut brugeren i fokus. Dette gør at vi hele tiden bliver tvunget til at tænke over hvordan vi laver den pågældende løsning så brugervenlig for målgruppen som overhovedet muligt. Men for mit vedkommende stopper denne tankegang ikke blot hos slut brugeren, men går helt ned til hvordan vi skaber et så brugervenligt udviklingsmilijø som overhovedet muligt. Min filisofi omkring dette er at jo mere brugervenligt dette er, des mindre fokus stjæler det fra den egentlige opgave.

Vores udviklingsmilijø hos Edentic består i dag af en række forskellige lag, der tilsammen får det hele til at gå op i en større enhed. Disse lag omfatter alt fra den bagvedliggende [Laravel](http://laravel.com) applikation der kører i en [Vagrant](http://vagrantup.com) box, til det javascript og SASS der skal bygges i front-enden.
Dette skaber et virvar af kommandoer som man som bruger skal huske. Til tider kan det derfor være svært at huske hvordan man f.eks. lige fik nulstillet databasen, eller hentet nye biblioteker fra Bower. Specielt er dette et problem hvis nye udviklere kommer til projektet løbende.

Jeg har derfor forsøgt at se på hvordan vi hos os kunne gøre det nemmere for brugerne at administrere udviklingsmilijøet, uden at skulle huske på flere lange kommandoer. Hertil har task manager værktøjet [Gulp](http://gulpjs.com/) været en stor hjælp, da det gør det muligt at lave et simpelt interface til at køre kommandoer fra.
Gulp har et stort bibliotek af plugins og et af disse plugins kaldet [gulp-shell](https://github.com/sun-zheng-an/gulp-shell) gør det muligt at få Gulp til at køre shell kommandoer.

Med dette plugin er det altså muligt at sætte det op således at man f.eks. kan lave en kommando der hedder `gulp dbreset`, der nulstiller databasen i udviklingsmilijøet. Alternativet for brugeren vil skulle være at logge ind i den virtuelle Vagrant maskine, hvorefter man skulle kører en række kommandoer på boksen. Ovenstående giver mulighed for at kører alt dette i en lille kommando linje der er til at huske.

Da vi hos Edentic ofte bygger løsninger i Laravel, har jeg derfor lavet en række Gulp kommandoer der kører en række ofte anvende Laravel Artisan kommandoer.

{% highlight javascript %}
var gulp = require('gulp'),
shell = require('gulp-shell');

//Vagrant commands

//Reset database and seed
gulp.task('dbreset', shell.task('vagrant ssh -c "php Code/artisan migrate:reset && php Code/artisan migrate --seed"'));

//Seed database
gulp.task('seed', shell.task('vagrant ssh -c "php Code/artisan migrate --seed"'));

//Run a composer install
gulp.task('composer', shell.task('vagrant ssh -c "cd Code && composer install"'));

//Run a composer update
gulp.task('composerupdate', shell.task('vagrant ssh -c "cd Code && composer update"'));

//Run PHPUnit tests
gulp.task('test', shell.task('vagrant ssh -c "cd Code && phpunit"'));

{% endhighlight %}

Til tider kan vi have behov for at tilføje nye JS plugins og composer biblioteker til projektet.
Når sådanne nye biblioteker tilføjes, skal de installaeres af alle brugere på projektet hvilket til tider kan skabe "broken builds". For at komme dette problem til undsæntning har jeg derfor lavet en gulp kommando der simpelt hedder `gulp virkerikke`. Denne kommando kører så alle de installations og opdaterings kmmandoer  den kender til, hvilket ofte får projektet til at virke igen. På den måde behøves man som bruger ikke at bruge ekstra meget tid på at finde ud af i hvilket system(Composer, Bower, Node) at den nye afhængighed er tilføjet. Igen noget der gør det nemmere at være bruger i vores udviklingsmilijø.

Personligt er jeg begyndt at blive mere og mere glad for Gulp aflene af den grund at det gør det muligt at administrere en rækkke større systemer fra et simpelt interface.