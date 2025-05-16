# Klausimas 2

Klausimas apie sekų diagramas.

Parodysiu dabartinę sekų diagrama, dabar jau realizuotą kodą ir kaip mes pakeistume tą sekų diagramą. Norime sužinote, ar teisingai galvojame ir kiek reikia/nereikia rodyti sekų diagramoje.

Dabartinė sekų diagrama:

![image](https://github.com/user-attachments/assets/e88efec2-3608-4534-9150-6d4e88f7ac4c)

## Kodas

Iš frontend puslpio iškviečiame tokį endpoint'ą:
```ts
const developerResponse = await DjangoBackend.get('/developer/info/');
```

> urls.py
```python
urlpatterns = [
    path('developer/info/', views.DeveloperController.DeveloperOwnView.as_view()),
]
```

Problema, kad iškviečiama klase, o ne funkcija, tai nelabai žinome ką daryti. Ji atrodo taip:

> DeveloperController.py
```python
class DeveloperInfoView(generics.RetrieveAPIView):
    serializer_class = DeveloperWithGamesSerializer
    permission_classes = [IsAuthenticated&(IsDeveloper|IsAdminUser)]

    def get_object(self):
        return get_object_or_404(Developer, user=self.request.user)
```
> DeveloperWithGamesSerializer.py
```python
class DeveloperWithGamesSerializer(serializers.ModelSerializer):
        games = NestedGameSerializer(read_only=True, many=True)

        class Meta:
            model = Developer
            fields = (
                'developer_name',
                'description',
                'country_established',
                'established_at',
                'games',
            )
```

Klasė grąžina kūrėją ir jo žaidimus.

## Klausimas

Taigi, šiuo metu DeveloperGamesPage iškviečia `4. retrieveAllDeveloperInfo()`. Tai yra neteisinga, nes DeveloperController neturi tokios funkcijos. Kviečiama yra klasė, kaip parodyta aukščiau.
Ar reiktų:
1. Tiesiog užrašyti klasės pavadinimą? Taptų `4. DeveloperInfoView()`
2. Viduje to `generics.RetrieveAPIView` kaip ir iškviečiama funkcija `retrieve()`, tai galbūt ją ir reiktų rašyti? Tada taptų `4. retrieve()`
3. Galbūt nei vienas iš tų variantų ir turite kitokį pasiūlymą?

Panašus klausimas ir su modeliais. Patys kaip ir nieko nekviečiame ant modelio, viską padaro pats `generics.RetreiveAPIView`. Tai ar 5 ir 7 būtų tikslinga pakeisti į `get()` tiesiog?

## Bonus Klausimas

![image](https://github.com/user-attachments/assets/c0b50605-3413-4b0d-be01-b56add2943ff)

Dėl ataskaitos 6.3 punkto.

Kaip reiktų pademonstruoti tą eiliškumą? Ar tiesiog labai daug nuotraukų sukelti iš realizacijos kodo ir sekų diagramų?

Ten parašyta, kad reikia parodyti vieną sudėtingos funkcijos atitikimą. Ar reikia *visą* sudėtingą funkciją parodyti? Ar užtenka kokius esminius fragmentus?
