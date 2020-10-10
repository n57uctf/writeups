# Train of Thought
Описание гласит:
 We&#39;ve managed to infiltrate Mr. Levenshtein&#39;s subconscious, but he keeps losing his train of thought! Sort out the noise and find the flag in this mess.

В описании к таску приложен файлик со словами. Вбивая в гугл Mr. Levenshtein находится [Levenshtein distance](https://en.wikipedia.org/wiki/Levenshtein_distance) и [калькулятор к нему](https://planetcalc.ru/1721/?language_select=ru) ~~хотя можно было и закодить.~~
Последовательно перебрав все слова, получился набор чисел, которые обозначают порядковый номер буквы латинского алфавита. 
Переведя их получаем `anorganizedmind`. Что имеет смысл и вставляется в структуру флага. 

```
flag{anorganizedmind}
```
# Troll Hunt

&gt;We&#39;ve identified a malicious troll who may be linked to a ransomware-esque virus. They&#39;ve been making posts using the hashtag &#34;#shrive&#34;. For now, just sift through the irrelevant junk and try to find another one of their accounts.

Ничего сложного, переходим в твиттер и вбиваем `#shrive` в поиск. Среди обычных постов находится недавно зарегистрированный [аккаунт](https://twitter.com/V760DHM) со странными постами. Самый первый из них ведет на [имгур](https://imgur.com/gallery/rdZ9RBz), в котором пост с пикчей трольфейса -- в комментах лежит флаг.
```
flag{shu7_up_4nd_d4nc3_G5jM30}
```
# Needle In A Haystack
&gt; Can you find the needle? Haystack [Link](https://mega.nz/file/5qBR3a7Z#VS7Uz6l2Jr1ZXcckQQaMvzMzuljpJsrfdfOFqSIfNSs)

По ссылке лежит архив с кучей `.txt` файлов. Задание и описание как бы намекает, что нужно найти флаг среди этих файлов. Простой скриптик на питоне с этим справляется.
```
flag{y0u_f0unD_Th3_n33d1e!}
```
# Reindeer Flotilla

 &gt;It&#39;s time to enter the Grid. Figure out a way to pop an alert() to get your flag http://chal.ctf.b01lers.com:3006


Переходим по ссылке и видим поле, которое выглядит как консоль. В консоли *браузера* пишем `alert()`. Всё.

```
flag{y0u_sh0uldnt_h4v3_c0m3_b4ck_flynn}
```

# First Day Inspection
```
It&#39;s your first day working at ENCOM, but they&#39;re asking you to figure things out yourself. What an onboarding process... take a look around and see what you can find.

http://chal.ctf.b01lers.com:3005
```
Переходим на сайт, открываем инструменты разработчика и видим первую часть флага. Далее - в консоли. Третья - в стилях `/css/styles.css`. Четвёртая - в скриптах `/js/script.js`. И пятая - в local storage.

```
flag{w3lc0m3_t0_ENC0M}
```

# Dream Stealing
```
I&#39;ve managed to steal some secrets from their subconscious, can you figure out anything from this?
Modulus: 98570307780590287344989641660271563150943084591122129236101184963953890610515286342182643236514124325672053304374355281945455993001454145469449640602102808287018619896494144221889411960418829067000944408910977857246549239617540588105788633268030690222998939690024329717050066864773464183557939988832150357227
One factor of N:  9695477612097814143634685975895486365012211256067236988184151482923787800058653259439240377630508988251817608592320391742708529901158658812320088090921919
Public key: 65537
Ciphertext: 75665489286663825011389014693118717144564492910496517817351278852753259053052732535663285501814281678158913989615919776491777945945627147232073116295758400365665526264438202825171012874266519752207522580833300789271016065464767771248100896706714555420620455039240658817899104768781122292162714745754316687483
```
Очень лёгкий шифр RSA. ~~дальше  хуже~~ Есть известный публичный ключ (факториал) N, остаётся только найти флаг перебором другого факториала (спасибо factordb, за то, что он есть) - phi. Это число делим c остатком на публичный ключ e (получаем d) и возводим шифротекст в степень d по модулю N, получем m. Печатаем флаг, как число m, преобразованное в строку (по байтам).  

```
from Crypto.Util.number import inverse, long_to_bytes
from factordb.factordb import FactorDB

#--------data--------#

N = 9695477612097814143634685975895486365012211256067236988184151482923787800058653259439240377630508988251817608592320391742708529901158658812320088090921919
e = 65537
c = 75665489286663825011389014693118717144564492910496517817351278852753259053052732535663285501814281678158913989615919776491777945945627147232073116295758400365665526264438202825171012874266519752207522580833300789271016065464767771248100896706714555420620455039240658817899104768781122292162714745754316687483

#--------factordb--------#

f = FactorDB(N)
f.connect()
factors = f.get_factor_list()

#--------rsa--------#

phi = 1
for factor in factors:
    phi *= factor - 1

d = inverse(e, phi)
m = pow(c, d, N)
flag = long_to_bytes(m).decode()

print(flag)

```
```
flag{4cce551ng_th3_subc0nsc10us}
```
