# lỗi syntax
- settings.php: ở dòng 7 đổi ; thành ]
- report.php: thêm dấu ; sau khi khai báo reportrows
- customers.php: thêm dấu ] sau 'active'
# Lỗi logic
1. Sai ở phần tính subtotal (syntax)

Ở file gốc:
```php
$subtotal = $products[$item['sku']]['price'] * $item['qty'];
```
Lỗi này sai bởi vì subtotal sẽ reset sau khi thêm cộng product mới chứ không cộng dồn

Cách sửa: thay dấu “=” bằng ”+=”
```php
$subtotal += $products[$item['sku']]['price'] * $item['qty']; (dòng 11 file checkout php)
```
2.	VAT đang tính trên subtotal gốc (logic)

Ở file gốc:
```php
$discountPercent = 0.1;
$discountValue = $subtotal * $discountPercent;
$shippingFee = $subtotal >= 50 ? 0 : 5;
$vat = $subtotal * 0.1;
$grandTotal = $subtotal - $discountValue + $shippingFee + $vat;
```
Tính số tiền lúc giảm giá nhưng lấy Vat gốc
Sửa:
```php
$discountValue = $subtotal * $discountPercent;
$afterDiscount = $subtotal - $discountValue;
$shippingFee = $afterDiscount >= 50 ? 0 : 5;
$vat = $afterDiscount * 0.1;
$grandTotal = $afterDiscount + $shippingFee + $vat; (checkout.php)
```
3. lỗi chỗ low stock 
File :
```php
foreach ($products as $sku => $product) {
    if ($product['stock'] < 1) {
        $lowStockItems[] = $sku . ' - ' . $product['name'];
    }
}
```
Giải thích: low stock nhưng đến 0  mới cảnh báo 
 
Sửa:
```php
$lowStockThreshold = 3;

foreach ($products as $sku => $product) {
	if ($product['stock'] <= $lowStockThreshold) {
	$lowStockItems[] = $sku . ' - ' . $product['name'];
	}
}
```
