# Próba ZH 2025.03.25.

Készítsen shell scriptet a következőkben leírt funkcionalitással pZH.sh néven. A fájlt lássa el futási joggal.

## Kettes szint

A program kérjen be a billentyűzetről (standard bemenetről) egy stringet és emellett fogadjon parancssori paraméterként tetszőleg mennyiségű fájlnevet is. Ha nem adtunk meg egyetlen fájlnevet sem, vagy ha a bekért string üres, akkor adjon hibajelzést, jelezve a hiba okát. Ha mindent rendben talált, akkor írja ki, hogy a megvizsgált fájlok hány sorában szerepel a bekért string. A program egyes funkcióit tesztelje is.


## Plusz egy jegy (A):

Írja ki azt is, hogy a megvizsgált fájlok hány sora egyezik meg ezzel a stringgel.

## Plusz egy jegy (B)

Írja ki azt is, hogy a megvizsgált fájlok hány sorában szerepel legalább kétszer a bekért stirng.

## Plusz egy jegy (C)

Írja ki azt is, hogy a megvizsgált fájlok hány sorában szerepel pontosan kétszer a bekért string.

## Plusz egy jegy (D)

Írja ki azt is, hogy a megvizsgált fájlok közül hány van olyan, amelyik pontosan a bekért szövegből áll és azon kívül nem tartalmaz semmi mást.

## Plusz egy jegy (E)

Készítsen összesítést a számlálásról, tehát ne csak külön-külön számoljon a fájlokban, hanem összesen is. (Csak az A, B, C pontok valamelyikével együtt érvényes.)

## Plusz egy jegy (F)

A program végezzen részletesebb hibaellenőrzést. Adjon figyelmeztető üzenetet (de ne álljon le), ha egy megadott fájl nem létezik, nem sima fájl, vagy a felhasználó számára nem engedélyezett annak olvasása.
```bash
#!/bin/bash

# Ellenőrizzük, hogy van-e megadott fájl
if [ $# -eq 0 ]; then
    echo "Nem adtál meg fájlt!"
    exit 1
fi

# Bekérjük a keresett sztringet
echo "Adjon meg egy stringet: "
read szo

# Ellenőrizzük, hogy a bekért string nem üres-e
if [ -z "$szo" ]; then
    echo "A megadott string üres!"
    exit 1
fi

# Segédváltozók az összesítéshez
ossz=0
osszl2=0
osszp2=0
i=0

# Fájlok feldolgozása
for fn in "$@"; do
    # Hibakezelés
    if [ ! -f "$fn" ]; then
        echo "A fájl '$fn' nem létezik, vagy nem sima fájl!"
        continue
    fi
    if [ ! -r "$fn" ]; then
        echo "A fájl '$fn' nem olvasható!"
        continue
    fi

    echo "A(z) '$fn' vizsgálata:"

    # Sorok számlálása, amelyek tartalmazzák a keresett stringet
    a=$(grep -c "$szo" "$fn")
    echo "A keresett string előforduló sorainak száma: $a"
    ((ossz+=a))

    # Sorok számlálása, amelyekben legalább kétszer szerepel a string
    b=$(grep -E "$szo.*$szo" "$fn" | wc -l)
    echo "A keresett string legalább kétszeri előfordulása: $b"
    ((osszl2+=b))

    # Sorok számlálása, amelyekben pontosan kétszer szerepel a string
    lketszer=$(grep -E "$szo.*$szo" "$fn" | wc -l)
    lharomszor=$(grep -E "$szo.*$szo.*$szo" "$fn" | wc -l)
    c=$((lketszer - lharomszor))
    echo "A keresett string pontosan kétszeri előfordulása: $c"
    ((osszp2+=c))

    # Fájl teljes egyezés vizsgálata
    if [ "$(cat "$fn")" == "$szo" ]; then
        ((i+=1))
    fi
done

# Összesítések kiírása
echo "Összes előfordulás: $ossz"
echo "Összes sor, ahol legalább kétszer szerepel: $osszl2"
echo "Összes sor, ahol pontosan kétszer szerepel: $osszp2"
echo "Teljes egyező fájlok száma: $i"


```
# ZH 2023.10.31.
# 2023.12.12

Készítsen shell scriptet ZH.sh néven. A fájlt lássa el futási joggal.

## Kettes szint

A program egy *kérjen be a billentyűzetről* egy szót. Keresse meg (*rekurzívan!*) az összes `.txt` kiterjesztésű fájlt. Minden fájlnak írja ki a nevét és a fájl neve alatt írja ki az adott fájl összes olyan sorát, amiben a megadott szó szerepel.

A program futtatásához hozzon létre egy olyan könyvtárszerkezetet, amiben van néhány olyan fájl, amiben a program *talál* egyező sort és és néhány olyan is, amiben nem.

## Plusz egy jegy (A)

A program számolja meg, hogy ha szerepel a fájlban a megadott szó, akkor *hány sorban* szerepel.

## Plusz egy jegy (B)

A program ne csak egy, hanem tetszőlegesen sok szót kérjen be és egyenként mindegyik szóra futtassa le a keresést.

## Plusz egy jegy (C)

A program azokat a sorokat keresse, amelyekben a keresett szó legalább kétszer szerepel.

## Plusz egy jegy (B)

A program ne az aktuális könyvtárban keressen, hanem *kérje be a billentyűzetről* azt a könyvtárat, ahol keresnie kell.

## Plusz egy jegy (E)

Ugyanaz, mint a *D*, csak tetszőlegesen sok könyvtárt meg tudjunk adni, ahol keresni fog a program.

```bash
#!/bin/bash

# Bekérünk egy vagy több keresendő szót
echo "Adjon meg egy vagy több keresendő szót, szóközzel elválasztva:"
read -r words

# Ellenőrizzük, hogy adott-e meg szavakat
if [ -z "$words" ]; then
    echo "Nem adott meg keresési szavakat!"
    exit 1
fi

# Bekérjük a keresési könyvtárakat
echo "Adja meg a keresési könyvtárakat szóközzel elválasztva:"
read -r directories

# Ellenőrizzük, hogy van-e megadott könyvtár
if [ -z "$directories" ]; then
    echo "Nem adott meg keresési könyvtárat!"
    exit 1
fi

# Bejárjuk az összes megadott könyvtárat és keresünk .txt fájlokat
for dir in $directories; do
    if [ ! -d "$dir" ]; then
        echo "A megadott könyvtár nem létezik: $dir"
        continue
    fi

    echo "Keresés a(z) '$dir' könyvtárban..."
    
    find "$dir" -type f -name "*.txt" | while read -r file; do
        echo "\nFájl: $file"
        
        for word in $words; do
            echo "Keresés a(z) '$word' szóra:"
            
            # Szó keresése a fájlban
            grep -Hn "$word" "$file"
            
            # Plusz egy jegy (A): Számláljuk meg, hány sor tartalmazza a keresett szót
            count=$(grep -c "$word" "$file" 2>/dev/null)
            echo "A(z) '$word' szó $count sorban szerepel ebben a fájlban."
            
            # Plusz egy jegy (C): Keresünk olyan sorokat, ahol a szó legalább kétszer szerepel
            count_double=$(grep -E "$word.*$word" "$file" | wc -l)
            echo "A(z) '$word' szó legalább kétszer szerepel $count_double sorban."
        done
    done
done


```

# ZH 2023.10.31.
# 2022.12.11

Készítsen shell scriptet pZH.sh néven. A fájlt lássa el futási joggal.

## Kettes szint

A program egy *parancssori paramétert* fogadjon. Keresse meg az összes `.txt` kiterjesztésű fájlt. Minden fájlnak írja ki a nevét és a fájl neve alatt jelezze, hogy a tartalmában szerepel-e az első paraméterben megadott szó.

A program futtatásához hozzon létre egy olyan könyvtárszerkezetet, amiben van néhány olyan fájl, amire a program *jelzést ad* és néhány olyan is, amire nem.

## Plusz egy jegy (A)

A program számolja meg, hogy ha szerepel a fájlban a megadott szó, akkor *hány sorban* szerepel.

## Plusz egy jegy (B)

A program jelezze ki, hogy van-e olyan sora az adott fájlnak, amiben a megadott szó legalább kétszer szerepel.

## Plusz egy jegy (C)

A program fogadjon *két parancssori paramétert* (mindkét paraméterben egy-egy szót lehet megadni) és jelölje meg azokat a fájlokat, amelyekben *van olyan sor*,
amiben *mindkét szó* szerepel.

## Plusz egy jegy (D)

A program ne az aktuális könyvtárban keressen, hanem *kérje be a billentyűzetről* azt a könyvtárat, ahol keresnie kell.

## Plusz egy jegy (E)

Ugyanaz, mint a *D*, csak tetszőlegesen sok könyvtárt meg tudjunk adni, ahol keresni fog a program.

```bash
#!/bin/bash

# Ellenőrizzük, hogy van-e legalább egy megadott paraméter
if [ $# -lt 1 ]; then
    echo "Használat: $0 <keresett szó> [második szó]"
    exit 1
fi

# Első keresett szó
word1="$1"
shift

# Második keresett szó (ha van)
word2="$1"
shift

# Bekérjük a keresési könyvtárakat
echo "Adja meg a keresési könyvtárakat szóközzel elválasztva:"
read -r directories

# Ellenőrizzük, hogy van-e megadott könyvtár
if [ -z "$directories" ]; then
    echo "Nem adott meg keresési könyvtárat!"
    exit 1
fi

# Bejárjuk az összes megadott könyvtárat és keresünk .txt fájlokat
for dir in $directories; do
    if [ ! -d "$dir" ]; then
        echo "A megadott könyvtár nem létezik: $dir"
        continue
    fi

    echo "Keresés a(z) '$dir' könyvtárban..."
    
    find "$dir" -type f -name "*.txt" | while read -r file; do
        echo "\nFájl: $file"
        
        # Keresés az első szóra
        grep -q "$word1" "$file"
        if [ $? -eq 0 ]; then
            echo "A(z) '$word1' szó szerepel ebben a fájlban."
            
            # Plusz egy jegy (A): Hány sorban szerepel a szó?
            count=$(grep -c "$word1" "$file")
            echo "A(z) '$word1' szó $count sorban szerepel ebben a fájlban."
            
            # Plusz egy jegy (B): Van-e olyan sor, ahol a szó legalább kétszer szerepel?
            count_double=$(grep -E "$word1.*$word1" "$file" | wc -l)
            echo "A(z) '$word1' szó legalább kétszer szerepel $count_double sorban."
        else
            echo "A(z) '$word1' szó nem szerepel ebben a fájlban."
        fi
        
        # Plusz egy jegy (C): Ha van második keresett szó, nézzük, hogy van-e olyan sor, ahol mindkettő szerepel
        if [ -n "$word2" ]; then
            grep -q "$word1.*$word2\|$word2.*$word1" "$file"
            if [ $? -eq 0 ]; then
                echo "Van olyan sor a fájlban, amelyben mindkét szó szerepel: '$word1' és '$word2'"
            else
                echo "Nincs olyan sor a fájlban, amelyben mindkét szó szerepelne."
            fi
        fi
    done
done

```
# ZH 2023.10.31.
# 2023.10.31

Készítsen shell scriptet ZH.sh néven. A fájlt lássa el futási joggal.

## Kettes szint
A program kérjen be három paramétert. Mindegyik paraméter egy-egy fájl neve. A program írja ki, hogy melyik fájlnak mekkora a mérete. A nagyobb méretű fájlok méretének kiíráshoz a megfelelő mértékegységet használja. Csak a fájl nevét és hosszát írja ki, más információt ne! A fájlok meglétét nem kell ellenőrizni.

## Plusz egy jegy (A)

A program minden esetben ellenőrizze a keresett fájlok meglétét. A hiányzó fájlokról tájékoztatta a felhasználót.

## Plusz egy jegy (B)

A program ne csak három, hanem tetszőlegesen sok bemenetet kezeljen.

## Plusz egy jegy (C)

A program számolja össze és írja ki, hogy hány fájlt talált meg és hányat nem.

## Plusz egy jegy (D)

Oldja meg, hogy a program az (egyébként billenyűzetről vett) bemeneteket fájlból tudja venni. Fedjen le minél több esetet!

## Plusz egy jegy (E)

A program írja ki, hogy összesen hány BÁJTBÓL állnak a megvizsgált fájlok.

## Plusz egy jegy (F)

A program dolgozzon fel egy parancssori paramétert is, ami egy szám. A program jelezze azokat a fájlokat, amelyek mérete nagyobb, vagy egyenlő ezzel (bájtban mérve).

```bash
#!/bin/bash

# Ellenőrizzük, hogy van-e legalább egy megadott fájl
if [ $# -lt 1 ]; then
    echo "Használat: $0 <fájl1> [fájl2 fájl3 ...] [méret_küszöb]"
    exit 1
fi

# Ha az utolsó paraméter szám, azt küszöbértéknek vesszük
last_param=${@: -1}
if [[ "$last_param" =~ ^[0-9]+$ ]]; then
    threshold=$last_param
    files=(${@:1:$#-1})
else
    threshold=0
    files=($@)
fi

found=0
not_found=0
total_size=0

for file in "${files[@]}"; do
    if [ -e "$file" ]; then
        ((found++))
        size=$(stat -c%s "$file")
        total_size=$((total_size + size))
        
        # Megfelelő mértékegység alkalmazása
        if [ $size -ge 1073741824 ]; then
            size_fmt=$(echo "scale=2; $size/1073741824" | bc)" GB"
        elif [ $size -ge 1048576 ]; then
            size_fmt=$(echo "scale=2; $size/1048576" | bc)" MB"
        elif [ $size -ge 1024 ]; then
            size_fmt=$(echo "scale=2; $size/1024" | bc)" KB"
        else
            size_fmt="$size B"
        fi
        
        echo "$file: $size_fmt"
        
        # Ha van küszöbérték, jelezzük, ha a fájl mérete nagyobb vagy egyenlő
        if [ $size -ge $threshold ]; then
            echo "-> $file nagyobb vagy egyenlő, mint a küszöbérték ($threshold B)"
        fi
    else
        ((not_found++))
        echo "$file nem található."
    fi
done

echo "Összes fájl mérete: $total_size B"
echo "Megtalált fájlok: $found"
echo "Nem található fájlok: $not_found"

```

# ZH 2023.10.30.
# 2023.10.30

Készítsen shell scriptet ZH.sh néven. A fájlt lássa el futási joggal.

## Kettes szint

A program kérjen be három paramétert. Eltérő mennyiségű paraméter esetén adjon hibajelzést és lépjen ki. Mindegyik paraméter egy-egy fájl neve. A program írja ki, hogy melyik fájlnak ki a tulajdonosa. Csak a fájl nevét és tulajdonosát írja ki, más információt ne! A fájlok meglétét nem kell ellenőrizni.

## Plusz egy jegy (A)

A program minden esetben ellenőrizze a keresett fájlok meglétét. A hiányzó fájlokról tájékoztassa a felhasználót.

## Plusz egy jegy (B)

A program ne csak három, hanem tetszőlegesen sok bemenetet kezeljen.

## Plusz egy jegy (C)

A program számolja össze és írja ki, hogy hány fájlt talált meg és hányat nem.

## Plusz egy jegy (D)

Oldja meg, hogy a program az (egyébként billenyűzetről vett) bemeneteket fájlból tudja venni. Fedjen le minél több esetet!

## Plusz egy jegy (E)

A program írja ki, hogy melyik tulajdonoshoz tartozik a legtöbb fájl.

## Plusz egy jegy (F)

A program dolgozzon fel egy parancssori paramétert is, ami egy felhasználó neve. A program írja ki, hogy mely fájlok tartoznak az így megadott felhasználóhoz és melyek nem.

```bash
#!/bin/bash

# Ellenőrizzük a paraméterek számát (legalább 3 kell, vagy fájlból olvasás)
if [ "$#" -lt 3 ] && [ "$1" != "-f" ]; then
    echo "Hiba: Pontosan három fájlnevet adj meg, vagy használd a -f opciót egy fájl megadásához!"
    exit 1
fi

# Ha fájlból kell olvasni
if [ "$1" == "-f" ]; then
    if [ ! -f "$2" ]; then
        echo "Hiba: A megadott fájl ($2) nem létezik!"
        exit 1
    fi
    files=($(cat "$2"))
else
    files=($@)
fi

total_found=0
total_missing=0
declare -A owner_count

echo "Fájltulajdonosok:"
for file in "${files[@]}"; do
    if [ -e "$file" ]; then
        owner=$(ls -l "$file" | awk '{print $3}')
        echo "$file: $owner"
        ((total_found++))
        ((owner_count[$owner]++))
    else
        echo "$file: NEM LÉTEZIK"
        ((total_missing++))
    fi
done

echo "Összes megtalált fájl: $total_found"
echo "Összes hiányzó fájl: $total_missing"

# Leggyakoribb tulajdonos meghatározása
most_files_owner=""
most_files_count=0
for owner in "${!owner_count[@]}"; do
    if [ ${owner_count[$owner]} -gt $most_files_count ]; then
        most_files_count=${owner_count[$owner]}
        most_files_owner=$owner
    fi
done

echo "Legtöbb fájllal rendelkező tulajdonos: $most_files_owner ($most_files_count fájl)"

# Ha van egy extra paraméter, mint felhasználónév
if [ "$1" != "-f" ] && [ "$#" -gt 3 ]; then
    user_check=${!#}
    echo "Fájlok, amelyek $user_check tulajdonában vannak:"
    for file in "${files[@]}"; do
        if [ -e "$file" ]; then
            owner=$(ls -l "$file" | awk '{print $3}')
            if [ "$owner" == "$user_check" ]; then
                echo "$file"
            fi
        fi
    done
fi

exit 0

```
