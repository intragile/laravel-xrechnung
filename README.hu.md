# Laravel XRechnung

Fejlesztő és karbantartó: [IntrAgile Kft.](https://intragile.eu)

[![Install](https://github.com/intragile/laravel-xrechnung/actions/workflows/ci.yml/badge.svg)](https://github.com/intragile/laravel-xrechnung/actions/workflows/ci.yml)
[![Latest Version](https://img.shields.io/packagist/v/intragile/laravel-xrechnung.svg)](https://packagist.org/packages/intragile/laravel-xrechnung)
[![PHP](https://img.shields.io/packagist/php-v/intragile/laravel-xrechnung.svg)](https://packagist.org/packages/intragile/laravel-xrechnung)
[![License](https://img.shields.io/packagist/l/intragile/laravel-xrechnung.svg)](LICENSE)

XRechnung 3.0 Laravelhez: német e-számlák (UBL és CII) ellenőrzése a hivatalos KoSIT-szabályokkal, és küldésük Peppol hálózaton.

Ez egy vékony metacsomag: nincs saját kódja, csak a [`intragile/laravel-einvoicing`](https://github.com/intragile/laravel-einvoicing) csomagot húzza be a használati esethez szükséges szabálycsomaggal együtt, hogy könnyen megtaláld. A teljes funkcionalitás ott van dokumentálva.

[English documentation → README.md](README.md)

## Követelmények

- PHP 8.3+ a `dom`, `libxml`, `mbstring` és `zlib` kiterjesztéssel
- Laravel 12 vagy 13

## Telepítés

```bash
composer require intragile/laravel-xrechnung
```

Telepítsd a Peppol validációs szabályokat (az alapértelmezett validátor akkor is betölti őket, ha csak XRechnung dokumentumot küldesz), és add meg a transportot, amelyen keresztül küldesz:

```bash
php artisan einvoicing:install-rules
php artisan migrate   # létrehozza a két táblát
```

```dotenv
EINVOICING_TRANSPORT=recommand
RECOMMAND_API_KEY_ID=key_...
RECOMMAND_API_SECRET=secret_...
RECOMMAND_COMPANY_ID=c_...
```

Ellenőrzés:

```bash
php artisan einvoicing:doctor
```

Használat:

```php
use IntrAgile\EInvoicing\Laravel\Facades\EInvoicing;

$xml = file_get_contents('xrechnung.xml');   // XRechnung 3.0 as UBL or CII

$report = EInvoicing::xrechnung()->validate($xml);   // KoSIT rules
if (! $report->isValid()) {
    throw new RuntimeException('The XRechnung is invalid.');
}

// Queued, validated again before sending, then delivered over Peppol.
$message = EInvoicing::send($xml, reference: 'RE-2026-0815');
```

A számla maga hordozza az útválasztást (feladó, címzett, folyamat) és az XRechnung folyamatvezérlést; a `send()` minden mást elutasít. A küldés Peppol hálózaton történik; ez a csomag nem küld Leitweg-ID portálra.

## Amit behúz

`intragile/laravel-einvoicing` (a Laravel-integráció, MIT), amely hozza a `intragile/php-einvoicing` motort (MIT) és az EN 16931 szabályokat, `intragile/einvoicing-rules-en16931` (EUPL-1.2), továbbá az `intragile/einvoicing-rules-xrechnung` csomagot (a KoSIT XRechnung-szabályok, Apache-2.0).

A metacsomag maga MIT licencű és nem szállít kódot; minden behúzott csomag a saját licencét tartja meg.

## Teljes dokumentáció

Teljes dokumentáció: [laravel-einvoicing](https://github.com/intragile/laravel-einvoicing#readme)

## Licenc

MIT. Lásd: [LICENSE](LICENSE).

---

Fejlesztő és karbantartó: [IntrAgile Kft.](https://intragile.eu)
