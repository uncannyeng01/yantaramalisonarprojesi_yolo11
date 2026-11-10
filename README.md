# yantaramalisonarprojesi_yolo11
yan taramalı sonar görüntülerinde görüntü iyileştirme yöntemlerinin hedef tespitine etkisinin incelenmesi projesi
# Yan Taramalı Sonar Görüntülerinde Görüntü İyileştirme Yöntemlerinin Hedef Tespitine Etkisinin İncelenmesi

Mühendislik Tasarım 3 – Proje ara raporu için deney kodları.
Hazırlayan: Erdem Işılay. Danışman: Doç. Dr. Alp Ertürk.

## Amaç

Yan taramalı sonar (SSS) görüntülerine uygulanan gürültü giderme yöntemlerinin (NLM, BM3D, Noise2Void, structN2V) YOLO11s ile insan hedefi tespitine etkisini incelemek. Her yöntem için eğitim, doğrulama ve test görüntülerinin hepsine aynı işlem uygulanmış, YOLO11s her hat için sıfırdan eğitilmiştir.

## Veri kümesi

AquaScan-1K Side Scan Sonar Dataset (v2), Zenodo: https://doi.org/10.5281/zenodo.18771165 (MIT lisansı).
Veri kümesi bu depoda yoktur, Zenodo'dan indirilmelidir. 1033 görüntü, tek sınıf (human), YOLO etiket biçimi.

Veri bölmesi gün bazındadır (aynı gün alınan görüntüler aynı kümede kalır): eğitim 722, doğrulama 155, test 155 görüntü. Eğitim kümesinden bozuk bir görüntü çıkarılmıştır.

## Not defterleri

| Dosya | İçerik |
| --- | --- |
| `[01_baseline_filtreler.ipynb]` | [veri indirme, gün bazlı bölme, baseline eğitimi, NLM ve BM3D hatları] |
| `[02_optuna_n2v.ipynb]` | [optuna iyileştirmesi ve araması, n2v hattı] |
| `[03_bm3d_sigma_parametreleri.ipynb]` | [bm3d filtresinde farklı sigma değerlerinin test kodları] |

## Ortak ayarlar

- Model: YOLO11s (Ultralytics), ön eğitimli `yolo11s.pt` ile başlatma
- Görüntü boyutu 960, batch 8, en fazla 100 epoch, patience 30, seed 0/1/2
- Model seçimi doğrulama kümesiyle yapılır, test kümesi yalnızca raporlama içindir
- NLM: OpenCV `fastNlMeansDenoisingColored`, h = 5/10/15 (ana hat h = 10)
- BM3D: `bm3d` paketi, σ = 10/20/30 (ana hat σ = 20), hızlandırılmış ayar (Y kanalı, sert eşikleme)
- Noise2Void / structN2V: CAREamics 0.2, yalnızca Y kanalı, 30 epoch, structN2V yatay maske 7 piksel
- Hiperparametre araması: Optuna (lr0, lrf, weight decay), 12 deneme

## Ortam

Google Colab, NVIDIA T4. Gerekli paketler: `ultralytics`, `opencv-python`, `bm3d`, `careamics`, `optuna`.

## Parametre ve sonuç dosyaları

`split.json`, `excluded_images.json`, `nlm_params.json`,`nlm_params_h5.json`,`nlm_params_h15.json`, `bm3d_params.json`,`bm3d_params_s10.json`,`bm3d_params_s30.json`,`n2v_params.json`,`n2v_struct_params.json`, `results_all_seeds.csv`, `results_n2v_struct.csv`, `results_n2v.csv`, `results_seeds.csv`, `results_table.csv`, `results_tarama_val.csv`.
