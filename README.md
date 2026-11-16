# Laravel XRechnung

Developed and maintained by [IntrAgile Kft.](https://intragile.eu)

[![Install](https://github.com/intragile/laravel-xrechnung/actions/workflows/ci.yml/badge.svg)](https://github.com/intragile/laravel-xrechnung/actions/workflows/ci.yml)
[![Latest Version](https://img.shields.io/packagist/v/intragile/laravel-xrechnung.svg)](https://packagist.org/packages/intragile/laravel-xrechnung)
[![PHP](https://img.shields.io/packagist/php-v/intragile/laravel-xrechnung.svg)](https://packagist.org/packages/intragile/laravel-xrechnung)
[![License](https://img.shields.io/packagist/l/intragile/laravel-xrechnung.svg)](LICENSE)

XRechnung 3.0 for Laravel: validate German e-invoices (UBL and CII) with the official KoSIT rules and send them over Peppol.

This is a thin metapackage: it has no code of its own and pulls in [`intragile/laravel-einvoicing`](https://github.com/intragile/laravel-einvoicing) together with the rule package this use case needs, so that you can find it. The full functionality is documented there.

[Magyar nyelvű leírás → README.hu.md](README.hu.md)

## Requirements

- PHP 8.3+ with `dom`, `libxml`, `mbstring`, `zlib`
- Laravel 12 or 13

## Quick start

```bash
composer require intragile/laravel-xrechnung
```

Install the Peppol validation rules (the default validator loads them even when you send only XRechnung documents) and set the transport you send through:

```bash
php artisan einvoicing:install-rules
php artisan migrate   # create the two tables
```

```dotenv
EINVOICING_TRANSPORT=recommand
RECOMMAND_API_KEY_ID=key_...
RECOMMAND_API_SECRET=secret_...
RECOMMAND_COMPANY_ID=c_...
```

Check the installation:

```bash
php artisan einvoicing:doctor
```

Usage:

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

The invoice carries its own routing (sender, receiver and process) and XRechnung process control; `send()` refuses anything else. Sending is over Peppol; this package does not submit to a Leitweg-ID portal.

## What it pulls in

`intragile/laravel-einvoicing` (the Laravel bridge, MIT), which brings the engine `intragile/php-einvoicing` (MIT) and the rule packages `intragile/einvoicing-rules-en16931` (EUPL-1.2), `intragile/einvoicing-rules-xrechnung` (Apache-2.0) and `intragile/einvoicing-rules-facturx` (Apache-2.0). `intragile/einvoicing-rules-xrechnung` is required directly as well.

The metapackage itself is MIT-licensed and ships no code; each pulled package keeps its own licence.

## Full documentation

Full documentation: [laravel-einvoicing](https://github.com/intragile/laravel-einvoicing#readme)

## License

MIT. See [LICENSE](LICENSE).

---

Developed and maintained by [IntrAgile Kft.](https://intragile.eu)
