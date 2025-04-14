# Klausimas

Projektas yra Django (su Django Rest Framework), kuris bus naudojamas kaip API. Front-end yra React.

## Puslapio atidarymas
>![Screenshot1](https://github.com/user-attachments/assets/4365e3b5-5d69-425d-b4b8-5d8aacabae67)<br/>
><sub>'Peržiūrėti kūrėjo žaidimus' sekų diagrama</sub>

1 punkte matome, kad yra iškviečiama `openDeveloperGames()` operacija, kuri priklauso 'HomePage'. Tuomet iškviečiama `navigate()` operacija, kuri priklauso 'Router' ir tai atidaro naują puslapį 'DeveloperGamesPage'.

```tsx
import { useNavigate } from "react-router";

function HomePage() {
    const navigate = useNavigate();

    function openDeveloperGames() {
        navigate('/developer/games');
    }

    return (
        <>
            <h1>HomePage</h1>
            <a onClick={openDeveloperGames} className="...">developer games</a>
        </>
    );
}

export default HomePage;
```

Kodo fragmente matome, kad kūrėjui paspaudus and nuorodos kūrėjas iškviečia `openDeveloperGames()` operaciją, tuomet įvyksta `navigate()` 'Router' opreacija ir kūrėjui yra atidaromas naujas puslapis.

Mano supratimu, šis kodo fragmentas atitinka aukščiau pateiktą sekų diagramą. Noriu sužinoti, ar sutinkate.

## Bendravimas su mūsų API
>![Screenshot1](https://github.com/user-attachments/assets/4365e3b5-5d69-425d-b4b8-5d8aacabae67)<br/>
><sub>'Peržiūrėti kūrėjo žaidimus' sekų diagrama. Nuotrauka ta pati, bet įdėta vėl, kad būtų patogiau matyti</sub>

Toliau žiūrime nuo 4 punkto. Ant 'DeveloperController' iškviečiamas `retrieveAllDeveloperInfo()`. Pas mus puslapyje tai įvyks čia:

```tsx
async function retrieveAllDeveloperInfo() {
    const response = await fetch(`http://localhost:8000/developer/${id}`);
}
```

Padromas HTTP request'as į tą nuorodą. Django Rest Framework pusėje šis request'as bus apdorotas `retrieveAllDeveloperInfo()` funkcijos.

Tuomet ant 'Developer' modelio  vyksta `retrieveDeveloperInfo()`. 'DeveloperController' toliau kviečia ir `retrieveDeveloperGames()` ant 'Game' modelio ir galiausiai visa ši informacija grįšta atgal į puslapį.

> urls.py
```py
urlpatterns = [
    path('developer/<int:pk>', views.retrieveAllDeveloperInfo),
]
```

> DeveloperController.py
```py
@api_view(['GET'])
def retrieveAllDeveloperInfo(request, pk):
    developer = Developer.objects.get(pk=pk)
    serializer = DeveloperDetailSerializer(developer)
    return Response(serializer.data)
```

Tačiau kaip matome iš kodo, vyksta visai ne tai. Didžiausias nesusipratimas man yra su modeliais. Jiems mes niekad nesukursime jokių operacijų, o naudosime in-built Django operacijas. Ar tada vietoje 5 punkto reiktų tiesiog rašyti `get()`, kadangi ant modelio kviečiame `get()` funkciją?

Taip pat tada neįvyksta 7 punktas išvis, kadangi žaidimai yra paimami kartu su kūrėju, nes taip aprašytas modelis. Tuomet viduje 'DeveloperDetailSerializer' šie duomenis formuojami iš atsiunčiami atgal. Ar 7 punkte irgi naudoti `get()` tada? Ar išvis panaikinti jį, kadangi viskas jau gaunama 5 punkte?

## Validacija
>![Screenshot2](https://github.com/user-attachments/assets/5a3fec46-81d3-4ee3-89aa-39966b7138a8)<br/>
><sub>'Redaguoti žaidimą' sekų diagrama</sub>

Dar toks papildomas klausimas apie validaciją. 15 punkte matome, kad naudojama operacija `checkIfMandatoryFieldsAreFilled()`. Activity diagramoje parodyta, jog vyks toks veiksmas, taigi sekų diagramoje atsirado ši operacija.

Tačiau pradėjus realizuoti projektą, paaiškėjo, kad tai ne visai tiesa. Formai įgyvendinti naudojama 'react-hook-forms' ir 'zod'. Nežinau kiek susipažinę esate su tokiais įrankiais, bet nesvarbu, jei nežinote, kas tai yra. Esmė tame, kad šios bibliotekos automatiškai padės validuoti formas, taigi patys jokios funkcijos neapsirašysime, kurią galėtume parodyti sekų diagramoje.

Problema tada tame:<br/>
Veiklos diagramoje rodoma, kad bus tikrinama, ar visi privalomi laukai užpildyti. Tai darysime ne mes, o biblioteka pati savaime. Tačiau sekų diagramoje tada nėra ką parodyti.

Nemanau, kad galime palikti tuščia toje vietoje sekų diagramoje, nes tada atrodys, kad nevyksta validacija, nors ji vyksta. Tai kaip daryti?
