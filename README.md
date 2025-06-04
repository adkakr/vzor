/* 1. Vytvorte vzor Builder, ktorý umožní vytvoriť reťazec zložený z operácií "+", "-", "*", "/" a z čísel.
Vytvorte triedu Program s metódou main a pomocou buildera vytvorte reťazec
"10 + 2 / 3 - 5". */

// Trieda ExpressionBuilder na budovanie matematického výrazu 
class ExpressionBuilder {
    private StringBuilder expression;

    public ExpressionBuilder(int number) {
        expression = new StringBuilder();
        expression.append(number);
    }

    public ExpressionBuilder add(int number) {
        expression.append(" + ").append(number);
        return this;
    }

    public ExpressionBuilder subtract(int number) {
        expression.append(" - ").append(number);
        return this;
    }

    public ExpressionBuilder multiply(int number) {
        expression.append(" * ").append(number);
        return this;
    }

    public ExpressionBuilder divide(int number) {
        expression.append(" / ").append(number);
        return this;
    }

    public String build() {
        return expression.toString();
    }
}

// Trieda Program s metódou main
public class Program {
    public static void main(String[] args) {
        ExpressionBuilder builder = new ExpressionBuilder(10);
        String result = builder
            .add(2)
            .divide(3)
            .subtract(5)
            .build();

        System.out.println(result);  
    }
}

// 2. Pre nasledujúce rozhranie a triedu naprogramujte adaptér, ktorý mapuje zapis na komprimuj a čítanie na dekomprimuj. Vhodne ošetrite výnimky.
/*public interface Subor {
    public void zapis(int[] data) throws IllegalStateException;
    public int[] citaj();
}

public class Kompresia {
    public void komprimuj(int[] data) throws Exception { }
    public int[] dekomprimuj() throws Exception { return new int[]{1, 2, 3}; }
}*/

public class KompresnyAdapter implements Subor {
    private Kompresia kompresia;

    public KompresnyAdapter() {
        this.kompresia = new Kompresia();
    }

    @Override
    public void zapis(int[] data) throws IllegalStateException {
        try {
            kompresia.komprimuj(data);
        } catch (Exception e) {
            throw new IllegalStateException("Chyba pri komprimácii: " + e.getMessage(), e);
        }
    }

    @Override
    public int[] citaj() {
        try {
            return kompresia.dekomprimuj();
        } catch (Exception e) {
            System.err.println("Chyba pri dekomprimácii: " + e.getMessage());
            return new int[0]; // alebo môžeš vrátiť null alebo vyhodiť RuntimeException
        }
    }
}

public class Program {
    public static void main(String[] args) {
        Subor subor = new KompresnyAdapter();

        try {
            subor.zapis(new int[]{10, 20, 30});
        } catch (IllegalStateException e) {
            System.err.println("Zápis zlyhal: " + e.getMessage());
        }

        int[] data = subor.citaj();
        for (int val : data) {
            System.out.println(val);
        }
    }
}

/*3. Vytvorte kompozíciu pre triedu MobilneZariadenie, ktorá je zložená z displeja, procesora a zoznamu senzorov. 
Displej nech má premennú popisujúcu uhlopriečku, procesor názov a počet jadier a senzor typ (reťazec). 
Pre triedu procesor implementujte metódu hashCode, ktorá zahŕňa všetky premenné. */

// Trieda Displej
class Displej {
    double uhlopriecka;

    public Displej(double uhlopriecka) {
        this.uhlopriecka = uhlopriecka;
    }
}

// Trieda Procesor s metódou hashCode
class Procesor {
    String nazov;
    int pocetJadier;

    public Procesor(String nazov, int pocetJadier) {
        this.nazov = nazov;
        this.pocetJadier = pocetJadier;
    }

    @Override
    public int hashCode() {
        int result = (nazov != null) ? nazov.hashCode() : 0;
        result = 31 * result + pocetJadier;
        return result;
    }
}

// Trieda Senzor
class Senzor {
    String typ;

    public Senzor(String typ) {
        this.typ = typ;
    }
}

// Trieda MobilneZariadenie
class MobilneZariadenie {
    Displej displej;
    Procesor procesor;
    Senzor[] senzory;

    public MobilneZariadenie(Displej displej, Procesor procesor, Senzor[] senzory) {
        this.displej = displej;
        this.procesor = procesor;
        this.senzory = senzory;
    }
}

// Testovacia trieda s metódou main()
public class Program {
    public static void main(String[] args) {
        Displej displej = new Displej(6.7);
        Procesor procesor = new Procesor("Exynos 2100", 8);
        Senzor[] senzory = {
            new Senzor("Akcelerometer"),
            new Senzor("Gyroskop"),
            new Senzor("Kompas")
        };

        MobilneZariadenie mobil = new MobilneZariadenie(displej, procesor, senzory);

        System.out.println("HashCode procesora: " + procesor.hashCode());
        System.out.println("Uhlopriecka displeja: " + mobil.displej.uhlopriecka);
        System.out.println("Typy senzorov:");
        for (Senzor s : mobil.senzory) {
            System.out.println("- " + s.typ);
        }
    }
}

/*4. Pre nasledujúcu triedu implementujte vzor Publish/Subscribe, ktorá upozorní pozorovateľov, keď sa prihlási alebo odhlási používateľ:
public class System {
    public void prihlasenie(String meno) { }
    public void odhlasenie(String meno) { }
} */

// Rozhranie pozorovateľa
interface Observer {
    void update(String sprava);
}

// Trieda oznamujúca udalosti (Publisher)
class PrihlasovaciSystem {
    Observer[] pozorovatelia = new Observer[10];
    int pocet = 0;

    public void pridajPozorovatela(Observer o) {
        if (pocet < pozorovatelia.length) {
            pozorovatelia[pocet] = o;
            pocet++;
        }
    }

    public void odstranPozorovatela(Observer o) {
        for (int i = 0; i < pocet; i++) {
            if (pozorovatelia[i] == o) {
                for (int j = i; j < pocet - 1; j++) {
                    pozorovatelia[j] = pozorovatelia[j + 1];
                }
                pozorovatelia[pocet - 1] = null;
                pocet--;
                break;
            }
        }
    }

    private void upozorniPozorovatelov(String sprava) {
        for (int i = 0; i < pocet; i++) {
            pozorovatelia[i].update(sprava);
        }
    }

    public void prihlasenie(String meno) {
        System.out.println(meno + " sa prihlásil.");
        upozorniPozorovatelov("Používateľ " + meno + " sa prihlásil.");
    }

    public void odhlasenie(String meno) {
        System.out.println(meno + " sa odhlásil.");
        upozorniPozorovatelov("Používateľ " + meno + " sa odhlásil.");
    }
}

// Hlavná trieda
public class Main {
    public static void main(String[] args) {
        PrihlasovaciSystem system = new PrihlasovaciSystem();

        // Pozorovateľ 1
        Observer logger = new Logger();
        system.pridajPozorovatela(logger);

        // Pozorovateľ 2
        Observer admin = new Admin();
        system.pridajPozorovatela(admin);

        system.prihlasenie("Zuzka");
        system.odhlasenie("Zuzka");
    }
}

// Implementácie pozorovateľov bez importu
class Logger implements Observer {
    public void update(String sprava) {
        System.out.println("[LOG] " + sprava);
    }
}

class Admin implements Observer {
    public void update(String sprava) {
        System.out.println("[ADMIN] " + sprava);
    }
}

/*5. Pre nasledujúcu triedu implementujte vzor Publish/Subscribe, ktorá upozorní pozorovateľov, keď sa pridá nový používateľ:
public class System {
    public void pridajPouzivatela(String meno) { }
} */

// Rozhranie Observer
interface Pozorovatel {
    void update(String meno);
}

// Trieda Publisher (oznamuje pridaného používateľa)
class UzivatelskySystem {
    private Pozorovatel[] pozorovatelia = new Pozorovatel[10];
    private int pocet = 0;

    public void pridajPozorovatela(Pozorovatel p) {
        if (pocet < pozorovatelia.length) {
            pozorovatelia[pocet] = p;
            pocet++;
        }
    }

    public void odstranPozorovatela(Pozorovatel p) {
        for (int i = 0; i < pocet; i++) {
            if (pozorovatelia[i] == p) {
                for (int j = i; j < pocet - 1; j++) {
                    pozorovatelia[j] = pozorovatelia[j + 1];
                }
                pozorovatelia[pocet - 1] = null;
                pocet--;
                break;
            }
        }
    }

    private void upozorniPozorovatelov(String meno) {
        for (int i = 0; i < pocet; i++) {
            pozorovatelia[i].update(meno);
        }
    }

    public void pridajPouzivatela(String meno) {
        System.out.println("Používateľ \"" + meno + "\" bol pridaný.");
        upozorniPozorovatelov(meno);
    }
}

public class Program {
    public static void main(String[] args) {
        UzivatelskySystem system = new UzivatelskySystem();

        // Pozorovateľ 1
        Pozorovatel logger = new Logger();
        system.pridajPozorovatela(logger);

        // Pozorovateľ 2
        Pozorovatel admin = new Admin();
        system.pridajPozorovatela(admin);

        // Pridanie používateľa
        system.pridajPouzivatela("Lenka");
    }
}

// Implementácia pozorovateľa – Logger
class Logger implements Pozorovatel {
    public void update(String meno) {
        System.out.println("[LOG] Pridaný používateľ: " + meno);
    }
}

// Implementácia pozorovateľa – Admin
class Admin implements Pozorovatel {
    public void update(String meno) {
        System.out.println("[ADMIN INFO] Nový používateľ: " + meno);
    }
}

/*6. Pre nasledujúcu triedu implementujte rozhranie Iterable tak, aby iterátor prechádzal iba párne čísla.
public class Cisla {
    private int[] cisla;

    public Cisla(int[] cisla) {
        this.cisla = cisla;
    }
} */

import java.util.Iterator;
import java.util.NoSuchElementException;

public class Cisla implements Iterable<Integer> {
    private int[] cisla;

    public Cisla(int[] cisla) {
        this.cisla = cisla;
    }

    @Override
    public Iterator<Integer> iterator() {
        return new ParneCislaIterator();
    }

    private class ParneCislaIterator implements Iterator<Integer> {
        private int index = 0;

        public ParneCislaIterator() {
            advanceToNextParne();
        }

        private void advanceToNextParne() {
            while (index < cisla.length && cisla[index] % 2 != 0) {
                index++;
            }
        }

        @Override
        public boolean hasNext() {
            return index < cisla.length;
        }

        @Override
        public Integer next() {
            if (!hasNext()) {
                throw new NoSuchElementException();
            }
            int result = cisla[index];
            index++;
            advanceToNextParne();
            return result;
        }
    }
}

public class Program {
    public static void main(String[] args) {
        int[] vstup = {1, 2, 3, 4, 5, 6};
        Cisla c = new Cisla(vstup);

        for (int i : c) {
            System.out.println(i);  // vypíše: 2, 4, 6
        }
    }
}

/*7.Pre nasledujúce rozhranie vytvorte vzor Decorator, ktorý bude mať premennú logovanie: boolean 
a ktorý vypíše na obrazovku správu pred a po volaní metód getMeno a zmenHeslo, ak je logovanie true.
public interface Pouzivatel {
    String getMeno();
    void zmenHeslo(String noveHeslo);
}*/

public class PouzivatelDecorator implements Pouzivatel {
    private Pouzivatel pouzivatel;
    private boolean logovanie;

    public PouzivatelDecorator(Pouzivatel pouzivatel, boolean logovanie) {
        this.pouzivatel = pouzivatel;
        this.logovanie = logovanie;
    }

    @Override
    public String getMeno() {
        if (logovanie) System.out.println("[LOG] Pred getMeno()");
        String meno = pouzivatel.getMeno();
        if (logovanie) System.out.println("[LOG] Po getMeno()");
        return meno;
    }

    @Override
    public void zmenHeslo(String noveHeslo) {
        if (logovanie) System.out.println("[LOG] Pred zmenHeslo()");
        pouzivatel.zmenHeslo(noveHeslo);
        if (logovanie) System.out.println("[LOG] Po zmenHeslo()");
    }
}

/* 8. Pre nasledujúcu triedu implementujte rozhranie Iterable, tak aby iterátor prechádzal čísla od zadu.
public class Cisla {
    private int[] cisla;

    public Cisla(int[] cisla) {
        this.cisla = cisla;
    }
} */
import java.util.Iterator;

public class Cisla implements Iterable<Integer> {
    private int[] cisla;

    public Cisla(int[] cisla) {
        this.cisla = cisla;
    }

    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<Integer>() {
            private int index = cisla.length - 1;

            @Override
            public boolean hasNext() {
                return index >= 0;
            }

            @Override
            public Integer next() {
                return cisla[index--];
            }
        };
    }
}


/* 9. Pre nasledujúcu triedu naprogramujte vzor Visitor, ktorý prechádza uzly pre-order, tzn. najprv sa prejde daný uzol a potom všetci jeho potomkovia. 
Triedu UzolVisitor naprogramujte tak, aby spojila do zoznamu všetky listy stromu (tzn. uzly bez potomkov).
import java.util.List;
import java.util.LinkedList;

public class Uzol {
    private int index;
    private List<Uzol> potomkovia = new LinkedList<>();

    public Uzol(int index) {
        this.index = index;
    }

    public int getIndex() {
        return index;
    }

    public List<Uzol> getPotomkovia() {
        return potomkovia;
    }
} */

public interface UzolVisitor {
    void navstiv(Uzol uzol);
}
public void akceptuj(UzolVisitor visitor) {
    visitor.navstiv(this);
}
import java.util.List;
import java.util.LinkedList;

public class ZbieracListovVisitor implements UzolVisitor {
    private List<Uzol> listy = new LinkedList<>();

    @Override
    public void navstiv(Uzol uzol) {
        if (uzol.getPotomkovia().isEmpty()) {
            listy.add(uzol);
        } else {
            for (Uzol potomek : uzol.getPotomkovia()) {
                potomek.akceptuj(this); // pre-order návšteva
            }
        }
    }

    public List<Uzol> getListy() {
        return listy;
    }
}

/* 10. Pre nasledujúce rozhranie a triedu naprogramujte adaptér, ktorý mapuje:

metódu zapis na zasifruj

metódu citaj na desifruj.

Vhodne ošetrite výnimky.

public interface Subor {
    public void zapis(int[] data) throws IllegalArgumentException;
    public int[] citaj() throws Exception;
}

public class Sifrovanie {
    public void zasifruj(int[] data) throws Exception {}
    public int[] desifruj() throws Exception { return new int[]{1, 2, 3}; }
} */

public class SifrovanieAdapter implements Subor {
    private Sifrovanie sifrovanie;

    public SifrovanieAdapter(Sifrovanie sifrovanie) {
        this.sifrovanie = sifrovanie;
    }

    @Override
    public void zapis(int[] data) throws IllegalArgumentException {
        if (data == null) {
            throw new IllegalArgumentException("Dáta nesmú byť null!");
        }
        try {
            sifrovanie.zasifruj(data);
        } catch (Exception e) {
            throw new IllegalArgumentException("Chyba pri šifrovaní: " + e.getMessage());
        }
    }

    @Override
    public int[] citaj() throws Exception {
        try {
            return sifrovanie.desifruj();
        } catch (Exception e) {
            throw new Exception("Chyba pri dešifrovaní: " + e.getMessage());
        }
    }
}

/* 11. Vytvorte kompozíciu pre triedu GrafickyRozhranie, ktoré je zložené z okien, a každé okno má zoznam ovládacích prvkov.

Okno nech má premennú popisujúcu polohu x a y a nadpis.

Ovládací prvok nech má odkaz na svoje okno a ID (reťazec).

Pre okno implementujte rozhranie Comparable, ktoré usporiada okná podľa x, a ak sa x rovná, tak podľa y. */

public class OvladaciPrvok {
    private String id;
    private Okno okno;  // odkaz na svoje okno

    public OvladaciPrvok(String id, Okno okno) {
        this.id = id;
        this.okno = okno;
    }

    public String getId() {
        return id;
    }

    public Okno getOkno() {
        return okno;
    }
}

import java.util.ArrayList;
import java.util.List;

public class Okno implements Comparable<Okno> {
    private int x;
    private int y;
    private String nadpis;
    private List<OvladaciPrvok> prvky = new ArrayList<>();

    public Okno(int x, int y, String nadpis) {
        this.x = x;
        this.y = y;
        this.nadpis = nadpis;
    }

    public void pridajPrvok(OvladaciPrvok prvok) {
        prvky.add(prvok);
    }

    public List<OvladaciPrvok> getPrvky() {
        return prvky;
    }

    @Override
    public int compareTo(Okno ine) {
        if (this.x != ine.x) {
            return Integer.compare(this.x, ine.x);
        } else {
            return Integer.compare(this.y, ine.y);
        }
    }

    public String getNadpis() {
        return nadpis;
    }
}

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class GrafickyRozhranie {
    private List<Okno> okna = new ArrayList<>();

    public void pridajOkno(Okno okno) {
        okna.add(okno);
    }

    public List<Okno> getOknaUsporiadane() {
        List<Okno> kopia = new ArrayList<>(okna);
        Collections.sort(kopia);
        return kopia;
    }
}

public class Main {
    public static void main(String[] args) {
        Okno okno1 = new Okno(100, 200, "Menu");
        Okno okno2 = new Okno(50, 150, "Nastavenia");
        Okno okno3 = new Okno(100, 100, "Hra");

        OvladaciPrvok p1 = new OvladaciPrvok("btn1", okno1);
        OvladaciPrvok p2 = new OvladaciPrvok("btn2", okno2);

        okno1.pridajPrvok(p1);
        okno2.pridajPrvok(p2);

        GrafickyRozhranie gui = new GrafickyRozhranie();
        gui.pridajOkno(okno1);
        gui.pridajOkno(okno2);
        gui.pridajOkno(okno3);

        for (Okno o : gui.getOknaUsporiadane()) {
            System.out.println("Okno: " + o.getNadpis());
        }
    }
}

/* 12 Vytvorte kompozíciu pre triedu Faktura, ktorá je zložená z:
položiek a adries príjemcu a odosielateľa.
Položky majú: cenu (číslo), popis (reťazec).
Adresy sú zložené z: ulice, PSČ, mesta.
Pre triedu Adresa implementujte metódu equals, ktorá porovnáva všetky premenné. */

public class Adresa {
    private String ulica;
    private String psc;
    private String mesto;

    public Adresa(String ulica, String psc, String mesto) {
        this.ulica = ulica;
        this.psc = psc;
        this.mesto = mesto;
    }

    public String getUlica() {
        return ulica;
    }

    public String getPsc() {
        return psc;
    }

    public String getMesto() {
        return mesto;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Adresa)) return false;

        Adresa a = (Adresa) o;
        return ulica.equals(a.ulica) &&
               psc.equals(a.psc) &&
               mesto.equals(a.mesto);
    }

    @Override
    public int hashCode() {
        return (ulica + psc + mesto).hashCode();
    }
}

public class Polozka {
    private double cena;
    private String popis;

    public Polozka(double cena, String popis) {
        this.cena = cena;
        this.popis = popis;
    }

    public double getCena() {
        return cena;
    }

    public String getPopis() {
        return popis;
    }
}

import java.util.ArrayList;
import java.util.List;

public class Faktura {
    private List<Polozka> polozky = new ArrayList<>();
    private Adresa odosielatel;
    private Adresa prijemca;

    public Faktura(Adresa odosielatel, Adresa prijemca) {
        this.odosielatel = odosielatel;
        this.prijemca = prijemca;
    }

    public void pridajPolozku(Polozka p) {
        polozky.add(p);
    }

    public List<Polozka> getPolozky() {
        return polozky;
    }

    public Adresa getOdosielatel() {
        return odosielatel;
    }

    public Adresa getPrijemca() {
        return prijemca;
    }
}
public class Main {
    public static void main(String[] args) {
        Adresa adr1 = new Adresa("Hlavná 1", "04001", "Košice");
        Adresa adr2 = new Adresa("Dlhá 5", "90001", "Bratislava");

        Faktura faktura = new Faktura(adr1, adr2);
        faktura.pridajPolozku(new Polozka(29.99, "Kancelárske kreslo"));
        faktura.pridajPolozku(new Polozka(4.50, "Ceruzky"));

        System.out.println("Faktúra má " + faktura.getPolozky().size() + " položky.");
        System.out.println("Adresa odosielateľa a príjemcu sú rovnaké: " +
            faktura.getOdosielatel().equals(faktura.getPrijemca()));
    }
}

/* 13 Vytvorte vzor Builder, ktorý umožní vytvoriť zoznam s vnorenými zoznamami hodnôt (tzn. metódy umožnia vložiť ďalší podzoznam a vložiť prvok do podzoznamu).
Vytvorte triedu Program s metódou main a pomocou buildera vytvorte nasledujúci zoznam:
[[1, 2], [3, 4, 5]] */

import java.util.ArrayList;
import java.util.List;

public class ZoznamBuilder {
    private List<List<Integer>> hlavnyZoznam = new ArrayList<>();
    private List<Integer> aktualnyPodzoznam = null;

    // Začne nový podzoznam
    public ZoznamBuilder novyPodzoznam() {
        aktualnyPodzoznam = new ArrayList<>();
        hlavnyZoznam.add(aktualnyPodzoznam);
        return this;
    }

    // Pridá prvok do aktuálneho podzoznamu
    public ZoznamBuilder pridaj(int cislo) {
        if (aktualnyPodzoznam == null) {
            throw new IllegalStateException("Najprv zavolaj novyPodzoznam()");
        }
        aktualnyPodzoznam.add(cislo);
        return this;
    }

    // Vráti výsledný zoznam
    public List<List<Integer>> postav() {
        return hlavnyZoznam;
    }
}

public class Program {
    public static void main(String[] args) {
        ZoznamBuilder builder = new ZoznamBuilder();

        List<List<Integer>> vysledok = builder
                .novyPodzoznam().pridaj(1).pridaj(2)
                .novyPodzoznam().pridaj(3).pridaj(4).pridaj(5)
                .postav();

        System.out.println(vysledok);  // Výstup: [[1, 2], [3, 4, 5]]
    }
}

/* 14. Vytvorte vzor Builder, ktorý umožní vytvoriť reťazec zložený z HTML elementov, tzn. metódy majú umožniť:
vložiť nový počiatočný element,
vložiť text,
vložiť koncový element.
Vytvorte triedu Program s metódou main a pomocou buildera vytvorte reťazec: 
"<p>text1<div>text2</div></p>"*/

public class HtmlBuilder {
    private StringBuilder sb = new StringBuilder();

    public HtmlBuilder zacniElement(String tag) {
        sb.append("<").append(tag).append(">");
        return this;
    }

    public HtmlBuilder text(String obsah) {
        sb.append(obsah);
        return this;
    }

    public HtmlBuilder konciElement(String tag) {
        sb.append("</").append(tag).append(">");
        return this;
    }

    public String postav() {
        return sb.toString();
    }
}

public class Program {
    public static void main(String[] args) {
        HtmlBuilder builder = new HtmlBuilder();

        String vysledok = builder
            .zacniElement("p")
                .text("text1")
                .zacniElement("div")
                    .text("text2")
                .konciElement("div")
            .konciElement("p")
            .postav();

        System.out.println(vysledok);  // Výstup: <p>text1<div>text2</div></p>
    }
}

/* 15. Pre nasledujúce rozhranie a triedu naprogramujte adaptér, ktorý mapuje metódu citaj() na metódu meraj() pričom:
skontroluje, či je teplomer zapnutý
ak nie je, zapne ho
Vhodne ošetrite výnimky.
public interface Senzor {
    public int stav();
    public int citaj() throws IllegalStateException;
}
public class Teplomer {
    private boolean zapnuty;

    public void zapni() { zapnuty = true; }

    public boolean stav() { return zapnuty; }

    public int meraj() throws Exception { return 10; }
} */

public class TeplomerAdapter implements Senzor {
    private Teplomer teplomer;

    public TeplomerAdapter(Teplomer teplomer) {
        this.teplomer = teplomer;
    }

    @Override
    public int stav() {
        return teplomer.stav() ? 1 : 0;
    }

    @Override
    public int citaj() throws IllegalStateException {
        if (!teplomer.stav()) {
            teplomer.zapni();  // automatické zapnutie
        }

        try {
            return teplomer.meraj();
        } catch (Exception e) {
            throw new IllegalStateException("Chyba pri meraní: " + e.getMessage());
        }
    }
}
public class Program {
    public static void main(String[] args) {
        Teplomer teplomer = new Teplomer();
        Senzor senzor = new TeplomerAdapter(teplomer);

        System.out.println("Stav: " + senzor.stav());
        System.out.println("Teplota: " + senzor.citaj());
        System.out.println("Stav po čítaní: " + senzor.stav());
    }
}


