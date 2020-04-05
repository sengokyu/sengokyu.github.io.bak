---
date: '2019-10-16T11:24:22+09:00'
layout: post
published: true
qiita_article_id: 211f09e342d7690613cf
tags:
- Azure
title: '[Microsoft] Azure 仮想マシン(VM)シリーズまとめ 2019-12版'
updated: '2019-12-28T09:20:46+09:00'

---
Azureの仮想マシンシリーズはいくつもあります。  
表にまとまっていると嬉しく思ったので、まとめてみます。  
現行バージョンの一部のみです。  
  
| シリーズ | CPU | 発表時期 |参考リンク |  
|:--|:--|:--|:--|  
| A_v2 || 2016-11 | https://blogs.technet.microsoft.com/jpitpro/2016/12/07/new-av2-series-vm-sizes/ |  
|B / Bms / Bs|Intel® Haswell 2.4 GHz E5-2673 v3|2017-9|https://azure.microsoft.com/en-us/blog/introducing-b-series-our-new-burstable-vm-size/|  
| DC | Intel® Xeon E-2176G 3.7GHz | 2018-11 ||  
| Dv2||||  
| DSv2 |Intel® Xeon E5-2673 v3 2.4 GHz |2017-3|https://devblogs.microsoft.com/azuregov/announcing-dsv2-series-and-fs-series-vms-for-azure-government/|  
| Dv3 |Intel® Broadwell E5-2673 v4 2.3GHz または　Intel® Haswell 2.4 GHz E5-2673 v3 | 2017-7 |https://blogs.technet.microsoft.com/jpitpro/2017/08/01/introducing-the-new-dv3-and-ev3-vm-sizes/|  
| DSv3 |〃|〃|〃|  
| Dav4 |AMD EPYC™ 7452 | 2019-11 （プレビュー）| https://azure.microsoft.com/en-us/blog/azure-iaas-for-every-workload/ |  
| Dasv4 |〃|〃|〃|  
| Ev3 |Intel® Broadwell E5-2673 v4 2.3GHz または　Intel® Haswell 2.4 GHz E5-2673 v3 | 2017-7 |https://blogs.technet.microsoft.com/jpitpro/2017/08/01/introducing-the-new-dv3-and-ev3-vm-sizes/|  
| ESv3 |〃|〃|〃|  
| Eav4 |AMD EPYC™ 7452|2019-11（プレビュー）|https://azure.microsoft.com/en-us/blog/azure-iaas-for-every-workload/|  
| Easv4 |〃|〃|〃|  
| F | Intel® Xeon E5-2673 v3 |2016-6|https://azure.microsoft.com/ja-jp/blog/f-series-vm-size/|  
| FS |〃|||  
| Fv2  |  Intel® Xeon Platinum 8168  |2017-10|https://blogs.technet.microsoft.com/jpitpro/2017/10/26/fv2-vms-are-now-available-the-fastest-vms-on-azure/|  
| FSv2 ||||  
| Lv2  | AMD EPYC™7551  | 2017-12 |https://azure.microsoft.com/en-us/blog/announcing-the-lv2-series-vms-powered-by-the-amd-epyc-processor/|  
|LSv2|AMD EPYC™7551 |2019-1|https://azure.microsoft.com/en-us/blog/announcing-the-general-availability-of-lsv2-series-azure-virtual-machines/|  
| M ||2017-12|https://azure.microsoft.com/ja-jp/updates/m-series-virtual-machines/|  
| Mv2 ||2019-10|https://azure.microsoft.com/ja-jp/updates/azure-mv2-series-vms-with-12tb-memory-now-ga-in-us-west-2-us-east-us-east-2-regions/|  
  
シリーズ名のお尻にある__v2__や__v3__は、世代を表します。  
__S__の有無は、Premium Diskサポートの有無を表します。  
  
# 変更履歴  
  
* 2019-10 初出  
* 2019-12 Dav4 Dasv4 Eav4 Easv4 シリーズを追加しました。  
