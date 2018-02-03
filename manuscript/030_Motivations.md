# Motywacja -- pierwszy krok w uczeniu się TDD

Piszę tę książkę, ponieważ jestem entuzjastą TDD. Wierzę, że TDD ma przewagę nad innymi sposobami wytwarzania oprogramowania, których używałem. 
Myślę, że wielu programistów podziela moje przekonania. To skłania mnie do zadania pytania - dlaczego więcej osób nie miałoby uczyć się TDD, dlaczego nie miałoby stosować TDD w swojej pracy? Wciąż nie mogę stwierdzić, że TDD jest głównym nurtem w wytwarzaniu oprogramowania. Podczas mojej kariery zawodowej nie widziałem wystarczająco dużo przykładów, które by potwierdzały taki stan rzeczy.

Drogi czytelniku! Już zdobyłeś mój szacunek, bo zdecydowałeś się sięgnąć po książkę, zamiast budować swoje rozumienie TDD na podstawie miejskich legend i swoich wyobrażeń. Nieważne, czy to Twoja pierwsza książka podejmująca temat TDD, czy też miałeś styczność z innymi - jestem zaszczycony i szczęśliwy, że dziś wybrałeś moją książkę. Mam nadzieję, że przeczytasz ją od deski do deski. Gdybyś jeszcze się wahał, chcę zadać Ci pomocnicze pytanie, które pomoże Ci stwierdzić, czy naprawdę masz ochotę na czytanie. Dlaczego właściwie chcesz się uczyć o TDD?

Kwestionując Twoją motywację, nie staram się zniechęcać Cię do czytania. Raczej chciałbym, byś dobrze wiedział co chcesz osiągnąć przeczytawszy moją książkę. Kilka lat temu uczyłem pewną osobę, która zainteresowała się TDD.  Zaczęliśmy razem pracować nad małym projektem, by ta osoba mogła nabyć niezbędnych umiejętności poprzez praktykę, ja zaś siedziałem obok niej, udzielając wskazówek. Tych lekcji było trzy, lub cztery - potem mój uczeń zrezygnował mając "pilniejsze rzeczy do zrobienia" i "nie mając czasu". Od tamtej pory, nie wykorzystywał TDD w pracy ani nie starał się zrozumieć niczego więcej. Nawet dzisiaj, zastanawiam się, co było jego motywacją i dlaczego ta wyparowała?

Z biegiem czasu zauważyłem, że niektórzy z nas (wliczając w to mnie) mogą sądzić, że "niestety muszą" nauczyć się czegoś (nie "pragną" tego) z różnych powodów, np. żeby dostać awans, zdobyć certyfikat, dodać coś do swojego CV, lub po prostu być na bieżąco z nowinkami. Niefortunnie, Test-Driven Development zdaje się należeć do kategorii "niestety muszę" dla wielu ludzi. Taki rodzaj motywacji będzie trudny do utrzymania na dłuższą metę.

Innym powodem dla którego można chcieć uczyć się TDD, są błędne oczekiwania. Niektórzy z nas mają mgliste wyobrażenie o rzeczywistych kosztach i zyskach, jakie daje TDD. Wiedząc, że jest cenione i chwalone przez innych, można wyciągnąć wnioski, że to będzie idealne rozwiązanie również dla nas. 
Dla przykładu, ktoś oczekujący poprawnie działającego kodu mógł usłyszeć, że dzięki TDD "kod staje się bardziej przetestowany". Jeśli nie mamy wiedzy, dlaczego warto wprowadzać TDD do projektu, możemy uważać, iż należy pisać najpierw testy przed kodem, po to tylko, by zapewnić 100% pokrycia kodu. Nie zrozumcie mnie źle - to częściowo może być prawdą, aczkolwiek w takim podejściu gubimy istotę TDD. Co więcej, gdy od TDD oczekiwaliśmy czegoś, czego ono nie daje, możemy być bardzo rozczarowani. Słyszałem wiele osób, które twierdziły "nie potrzebuję TDD, bo muszę mieć testy systemowe dające większą pewność, co do poprawnego działania produktu" albo "po co mi testy jednostkowe[^notonlyunittests] kiedy już mam testy integracyjne, smoke-testy, sanity-testy, exploration-testy, etc...?". Wiele razy byłem świadkiem, że porzucano idee TDD zanim ją w ogóle zrozumiano.

Czy nauka TDD ma dla Ciebie priorytet? Czy jesteś zdeterminowany, by wypróbować TDD i naprawdę się tego nauczyć? Jeśli jest inaczej - hej - słyszałem, że nowy sezon "Gry o Tron" pojawił się w telewizji, czemu nie miałbyś zająć się właśnie nim? Dobra, tylko się przekomarzam. 
Mówi się, że zasady TDD są łatwe do zrozumienia, ale ciężko być prawdziwym ekspertem ("easy to learn, hard to master"[^easytolearn]). Dlatego, bez odrobiny odwagi, będzie ciężko. Szczególnie, że zamierzam wprowadzać Cię w temat powoli, stopniowo, byś otrzymał lepsze wyjaśnienie różnych technik i sposobów pisania.

Jakie jest TDD?
------------------

Razem z bratem lubiliśmy grać w gry video gdy byliśmy dziećmi -- szczególnie miło wspominam grę Tekken 3 -- japoński beat'em up na Sony Playstation (PSX). Ukończenie gry wszystkimi zawodnikami i odblokowanie wszystkich ukrytych bonusów, mini-gier etc. zajmowało jeden dzień. Ktoś mógłby powiedzieć, że od tego momentu gra nie oferuje niczego więcej. Dlaczego więc, z bratem, graliśmy w nią ponad rok?

![Tekken3](images/Tekken3-gray.png)

To dlatego, że każdy wojownik w grze posiadał mnóstwo kombosów, kopnięć i uderzeń rękami, które można było łączyć na różne sposoby. Niektóre dało się zastosować tylko w określonych sytuacjach, inne mogłem użyć prawie zawsze bez ryzyka narażenia się na kontratakt. Mogłem zejść z lini ataku przeciwnika, a także byłem w stanie wykopać przeciwnika w powietrze, gdzie nie mógł już blokować moich ciosów, a potem wykonać dodatkowy atak zanim upadł na ziemię. Ta powietrzna technika nazywa się "juggles". W tamtych czasach pojawiały się czasopisma, które każdego miesiąca publikowały listę nowo odkrytych "juggles", nie pozwalając - w ten spoób - zgasnąć fascynacji graczy przez ponad rok.

Tak, łatwo było się nauczyć grać w Tekken -- mogłem poświęcić zaledwie jedną godzinę trenując najważniejsze ruchy postaci i byłem w stanie już "używać" danego zawodnika, ale wiedziałem, że lepiej bym walczył gdybym zdobył doświadczenie i wiedzę, które techniki są ryzykowne, a które nie, których ataków używać w określonych sytuacjach, jak łączyć je ze sobą, jak zmniejszyć możliwość kontrataku. Nic dziwnego, że wkrótce pojawiło się wiele turniejów, gdzie gracze mogli walczyć o chwałę, sławę i nagrody. Nawet dziś, można obejrzeć niektóre z tych legendarnych pojedynków na YouTube.

TDD jest jak Tekken. Prawdopodobnie słyszałeś pojęcie "red-green-refactor" lub ogólną zasadę "napisz najpierw test, potem kod", może nawet przeprowadziłeś eksperyment, gdzie próbowałeś zaimplementować sortowanie bąbelkowe lub jakąś inną rzecz zaczynając od napisania testu. To wszystko jest jak uczenie się Tekkena przez sprawdzanie każdego ataku na przeciwniku, który nie może się ruszać, bez całego kontekstu realnej gry, który czyni grę naprawdę wymagającą. Chociaż uważam takie ćwiczenia "na sucho" za bardzo użyteczne (sam zrobiłem dużo takich), to największe korzyści daje zrozumienie, jak TDD jest używane w prawdziwym życiu.

Niktórzy ludzie z którymi rozmaiwam o TDD określają to, co do nich mówię, jako naprawdę demotywujące -- "jest tak wiele rzeczy, na które muszę uważać, że ​​nigdy nie chcę zaczynać!" Luz, nie panikuj -- przypomnij sobie, jak po raz pierwszy próbowałeś jeździć na rowerze -- nie zaprzątałeś sobie głowy tym, że musisz znać jakieś przepisy drogowe i że trzeba przestrzegać znaków drogowych, to Cię wcale nie powstrzymało, prawda?

Uważam, że TDD jest fascynujące i w ogóle sprawia, że pisanie kodu ekscytuje. Niektórzy faceci w moim wieku już myślą, że wiedzą wszystko o kodowaniu, nudzą się z tym i nie mogą się doczekać, aż przejdą do "menadżerki", wymagań lub analizy biznesowej, ale hej! Oto pojawia się nowy zestaw technik, które sprawią, że moja kariera programisty znów będzie wyzwaniem! Mogę nabyć umiejętności, które będę w stanie zastosować podczas pracy z wieloma różnymi technologiami i językami, a to uczyni mnie lepszym programistą! Czy to nie jest coś, do czego warto dążyć?

## Zaczynajmy !

W tym rozdziale próbowałem sprowokować Cię do przemyślenia swojej postawy i motywacji. Jeśli nadal jesteś zdeterminowany by nauczyć się TDD czytając tę ​​książkę (a mam nadzieję, że tak), to zacznijmy pracę!

[^easytolearn]: Nie wiem, kto pierwszy to powiedział, przeszukałem Internet i znalazłem ten zwrot w kilku miejscach, gdzie żaden z piszących nie informował, kogo cytuje - więc postanowiłem tylko wspomnieć, że nie jestem autorem tych słów.

[^notonlyunittests]: Nawiasem mówiąc, TDD to nie tylko testy jednostkowe, jeszcze do tego dojdziemy.

