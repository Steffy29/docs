---
title: Konfiguracja rozwiązania Network Firewall
excerpt: Dowiedz się, jak skonfigurować reguły firewalla w OVH
slug: network-firewall
section: Sieć & IP
---

**Ostatnia aktualizacja dnia 2018-01-18**

## Wprowadzenie

Aby chronić swoją globalną infrastrukturę oraz serwery klientów, OVH udostępniło zaporę ogniową, z możliwością wprowadzenia własnej konfiguracji, w pełni zintegrowaną z rozwiązaniem **Anty-DDoS** (VAC): Network Firewall. Jest to rozwiązanie filtrujące ataki z sieci publicznej na usługi naszych klientów.

**Ten przewodnik wyjaśnia, jak skonfigurować zaporę Network Firewall w Panelu klienta**.

> [!primary]
>
> VAC* : Więcej informacji o VAC - systemie ochrony przed atakami DDoS, znajdziesz na stronie: <https://www.ovh.pl/anti-ddos/>.
> 

![Szczegółowe informacje na temat VAC](images/vac-inside.png){.thumbnail}

## Wymagania początkowe

- Korzystanie z usługi OVH ze zintegrowaną zaporą ogniową, Network Firewall: ([Serwer dedykowany](https://www.ovh.pl/serwery_dedykowane/){.external}, [VPS](https://www.ovh.pl/vps/),{.external} [instancje Public Cloud](https://www.ovh.pl/public-cloud/instances/){.external}, [Private Cloud](https://www.ovh.pl/private-cloud/){.external}, [IP Failover](https://www.ovh.pl/serwery_dedykowane/ip_failover.xml){.external}...)
- Dostęp do [Panelu klienta OVH](https://www.ovh.com/auth/?action=gotomanager){.external}.
- Posiadanie podstawowej wiedzy o sieciach.

## W praktyce

### Aktywacja Network Firewall

> [!primary]
>
Network Firewall chroni adresy IP powiązane z serwerem. Należy zatem skonfigurować reguły dla każdego adresu IP oddzielnie. Wprowadzenie wspólnej konfiguracji dla całego serwera nie jest możliwe.
> 

Aktywację i konfigurację wykonuje się ręcznie z Panelu klienta, w sekcji `IP`{.action}, klikając ikonę koła zębatego, umieszczoną po prawej stronie danego adresu IPv4.

![Aktywacja Network Firewall](images/firewall_creation.png){.thumbnail}

- Następnie wymagane jest potwierdzenie.

![Potwierdzenie](images/creationvalid.png){.thumbnail}

- Następnie wystarczy kliknąć `Aktywuj firewall`{.action} i skonfigurować go, korzystając z przycisku`Skonfiguruj firewall`{.action}, ukrytego pod ikoną koła zębatego, obok adresu IPv4:

![Aktywacja konfiguracji](images/activationconfig.png){.thumbnail}

Do dyspozycji masz **20 reguł dla każdego adresu IP**.


> [!warning]
>
> Firewall uruchamia się automatycznie przy każdym ataku DDoS i nie można go wyłączyć przed zakończeniem ataku. Dlatego ważne jest, aby reguły firewalla były aktualne.
> Domyślnie, żadne reguły nie są skonfigurowane, więc można ustanowić dowolne połączenia z serwerem.
> Kiedy korzystasz z firewalla, pamiętaj o regularnym sprawdzaniu reguł (jeśli je ustawiłeś), nawet jeśli jest on w danej chwili dezaktywowany.
> 

> [!primary]
>
> - Fragmentacja UDP jest domyślnie zablokowana (DROP). Jeśli używasz sieci VPN, to podczas aktywacji firewalla, pamiętaj, aby poprawnie skonfigurować maksymalną jednostkę transmisji (MTU). Na przykład na OpenVPN możesz zaznaczyć `MTU test`{.action}.
> - Reguły skonfigurowane w Network Firewallu nie są brane pod uwagę wewnątrz sieci OVH. Wprowadzone reguły nie mają wpływu na połączenia w wewnętrznej sieci OVH.
>


### Konfiguracja Network Firewall

Dodawanie reguły odbywa się przez kliknięcie po prawej stronie `Dodaj regułę`{.action}:

![Dodaj regułę](images/ajoutregle1.png){.thumbnail}

Dla każdej reguły możesz wybrać:

- priorytet (od 0 do 19, gdzie 0 jest pierwszą zastosowaną regułą, a następnie inne);
- akcja (`Zezwalaj`{.action} lub `Odrzucaj`{.action});
- protokół;
- IP (opcjonalnie);
- port źródłowy (tylko dla TCP);
- port docelowy (tylko dla TCP);
- opcje TCP (tylko dla TCP).


![Szczegóły dodawania reguły](images/ajoutregle4.png){.thumbnail}


> [!primary]
>
> - Priorytet 0: zaleca się autoryzację protokołu TCP we wszystkich IP z opcją `Established`{.action}. Opcja `Established`{.action} umożliwia sprawdzenie czy pakiet jest częścią poprzednio otwartej sesji (już zainicjowanej). Jeśli na to nie zezwolisz, serwer nie otrzyma zwrotów protokołu TCP z żądań SYN/ACK.
> - Priorytet 19: odrzucenie całego protokołu IPv4, jeśli nie zostanie spełniona żadna reguła przed 19-tą (ostatnią możliwą).
> 


### Przykład konfiguracji

Aby pozostawić otwarte tylko porty SSH (22), HTTP (80), HTTPS (443), UDP (na porcie 10000), zezwalając na ICMP, należy przestrzegać następujących zasad:

![Przykład konfiguracji](images/exemple.png){.thumbnail}

Reguły są uporządkowane chronologicznie, od 0 (pierwsza odczytana reguła) do 19 (ostatnia odczytana reguła) i w tym porządku są uruchamiane dla pakietów.  Reguły przestają być sprawdzane, w chwili gdy jedna z nich dotyczy odebranego pakietu.

Na przykład pakiet przeznaczony dla portu 80/TCP zostanie przechwycony przez regułę 2, wtedy kolejne reguły nie są już aplikowane. Pakiet przeznaczony dla portu 25/TCP zostanie przechwycony tylko przy ostatniej regule (19), która zablokuje go, ponieważ OVH nie zezwala na żadną komunikację na porcie 25 w poprzednich regułach.

> [!warning]
>
> W chwili gdy włącza się ochrona Anty-DDoS, Twoje reguły zdefinowane w usłudze Network Firewall zostaną uaktywnione, nawet jeśli je wyłączyłeś. W przypadku dezaktywacji firewalla, pamiętaj o usunięciu reguł.
> 

## Sprawdź również

Przyłącz się do społeczności naszych użytkowników na stronie <https://community.ovh.com/en/>.
