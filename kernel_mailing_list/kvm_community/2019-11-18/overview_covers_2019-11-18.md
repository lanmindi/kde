

#### [PATCH V12 0/6] mdev based hardware virtio offloading support
##### From: Jason Wang <jasowang@redhat.com>

```c
From patchwork Mon Nov 18 06:16:57 2019
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Jason Wang <jasowang@redhat.com>
X-Patchwork-Id: 11248907
Return-Path: <SRS0=wTpQ=ZK=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 5275714C0
	for <patchwork-kvm@patchwork.kernel.org>;
 Mon, 18 Nov 2019 06:18:13 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id 1D6402071E
	for <patchwork-kvm@patchwork.kernel.org>;
 Mon, 18 Nov 2019 06:18:13 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (1024-bit key) header.d=redhat.com header.i=@redhat.com
 header.b="eKx8RJV8"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726506AbfKRGRu (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Mon, 18 Nov 2019 01:17:50 -0500
Received: from us-smtp-1.mimecast.com ([207.211.31.81]:27323 "EHLO
        us-smtp-delivery-1.mimecast.com" rhost-flags-OK-OK-OK-FAIL)
        by vger.kernel.org with ESMTP id S1726347AbfKRGRt (ORCPT
        <rfc822;kvm@vger.kernel.org>); Mon, 18 Nov 2019 01:17:49 -0500
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=redhat.com;
        s=mimecast20190719; t=1574057867;
        h=from:from:reply-to:subject:subject:date:date:message-id:message-id:
         to:to:cc:cc:mime-version:mime-version:content-type:content-type:
         content-transfer-encoding:content-transfer-encoding;
        bh=YNqgqhzyPN9huNmuMw3zZZeQPX41OJUwoAnZ3BjhV9c=;
        b=eKx8RJV8VoPLhrkLcRkAh4UidEiLGc9rCVzR+5suUk3eW+KBI6meJpWyNgu7TU9Ry9o4Kb
        iV+r4v4AcVI/XcXvimX3UVdPQdElwzUJee+baxD80m3d7rx8USlK5eiEyynpOxEi2VYGzG
        niknKXLrO5HLY3uKNnkrtzjry/M2QOA=
Received: from mimecast-mx01.redhat.com (mimecast-mx01.redhat.com
 [209.132.183.4]) (Using TLS) by relay.mimecast.com with ESMTP id
 us-mta-169-b2k1ERQ-MaeiYjOvpGKg6g-1; Mon, 18 Nov 2019 01:17:46 -0500
Received: from smtp.corp.redhat.com (int-mx02.intmail.prod.int.phx2.redhat.com
 [10.5.11.12])
        (using TLSv1.2 with cipher AECDH-AES256-SHA (256/256 bits))
        (No client certificate requested)
        by mimecast-mx01.redhat.com (Postfix) with ESMTPS id AE1221005512;
        Mon, 18 Nov 2019 06:17:40 +0000 (UTC)
Received: from jason-ThinkPad-X1-Carbon-6th.redhat.com
 (ovpn-12-215.pek2.redhat.com [10.72.12.215])
        by smtp.corp.redhat.com (Postfix) with ESMTP id 7106460BF4;
        Mon, 18 Nov 2019 06:17:06 +0000 (UTC)
From: Jason Wang <jasowang@redhat.com>
To: kvm@vger.kernel.org, linux-s390@vger.kernel.org,
        linux-kernel@vger.kernel.org, dri-devel@lists.freedesktop.org,
        intel-gfx@lists.freedesktop.org,
        intel-gvt-dev@lists.freedesktop.org, kwankhede@nvidia.com,
        alex.williamson@redhat.com, mst@redhat.com, tiwei.bie@intel.com
Cc: virtualization@lists.linux-foundation.org, netdev@vger.kernel.org,
        cohuck@redhat.com, maxime.coquelin@redhat.com,
        cunming.liang@intel.com, zhihong.wang@intel.com,
        rob.miller@broadcom.com, xiao.w.wang@intel.com,
        haotian.wang@sifive.com, zhenyuw@linux.intel.com,
        zhi.a.wang@intel.com, jani.nikula@linux.intel.com,
        joonas.lahtinen@linux.intel.com, rodrigo.vivi@intel.com,
        airlied@linux.ie, daniel@ffwll.ch, farman@linux.ibm.com,
        pasic@linux.ibm.com, sebott@linux.ibm.com, oberpar@linux.ibm.com,
        heiko.carstens@de.ibm.com, gor@linux.ibm.com,
        borntraeger@de.ibm.com, akrowiak@linux.ibm.com,
        freude@linux.ibm.com, lingshan.zhu@intel.com, eperezma@redhat.com,
        lulu@redhat.com, parav@mellanox.com,
        christophe.de.dinechin@gmail.com, kevin.tian@intel.com,
        stefanha@redhat.com, rdunlap@infradead.org, hch@infradead.org,
        gregkh@linuxfoundation.org, jgg@mellanox.com,
        Jason Wang <jasowang@redhat.com>
Subject: [PATCH V12 0/6] mdev based hardware virtio offloading support
Date: Mon, 18 Nov 2019 14:16:57 +0800
Message-Id: <20191118061703.8669-1-jasowang@redhat.com>
MIME-Version: 1.0
X-Scanned-By: MIMEDefang 2.79 on 10.5.11.12
X-MC-Unique: b2k1ERQ-MaeiYjOvpGKg6g-1
X-Mimecast-Spam-Score: 0
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Hi all:

There are hardwares that can do virtio datapath offloading while
having its own control path. This path tries to implement a mdev based
unified API to support using kernel virtio driver to drive those
devices. This is done by introducing a new mdev transport for virtio
(virtio_mdev) and register itself as a new kind of mdev driver. Then
it provides a unified way for kernel virtio driver to talk with mdev
device implementation.

Though the series only contains kernel driver support, the goal is to
make the transport generic enough to support userspace drivers. This
means vhost-mdev[1] could be built on top as well by resuing the
transport.

A sample driver is also implemented which simulate a virito-net
loopback ethernet device on top of vringh + workqueue. This could be
used as a reference implementation for real hardware driver.

Also a real IFC VF driver was also posted here[2] which is a good
reference for vendors who is interested in their own virtio datapath
offloading product.

Consider mdev framework only support VFIO device and driver right now,
this series also extend it to support other types. This is done
through decoupling VFIO specific bits out of mdev core and make mdev
an independent module that allows to be used by multiple types of
buses.

Pktgen test was done with virito-net + mvnet loop back device.

Please review.

[1] https://lkml.org/lkml/2019/11/5/424
[2] https://lkml.org/lkml/2019/11/5/227

Changes from V11:
- decouple VFIO specific bits out of mdev core
- make mdev an indepdent module to allow buses other than VFIO mdev
- allow structure composition of mdev through specifiy the size of
  mdev structure
- introduce mdev_vfio structure and store the VFIO specific callbacks
  there
- don't use "mdev" bus for virtio, use a new "mdev_virtio" bus, and
  store the virtio specific callbacks in mdev_virtio structure.
- do the class_id, matching on top of "mdev_virtio" bus

Changes from V10:
- rename mvnet to mvnet_loopback
- fix typo in the help text for sample Kconfig

Changes from V9:
- Tweak the help text for virito-mdev kconfig

Changes from V8:
- try silent checkpatch, some are still there becuase they were inherited
  from virtio_config_ops which needs to be resolved in an independent series
- tweak on the comment and doc
- remove VIRTIO_MDEV_F_VERSION_1 completely
- rename CONFIG_VIRTIO_MDEV_DEVICE to CONFIG_VIRTIO_MDEV

Changes from V7:
- drop {set|get}_mdev_features for virtio
- typo and comment style fixes

Changes from V6:
- rename ops files and compile guard

Changes from V5:
- use dev_warn() instead of WARN(1) when class id is not set
- validate id_table before trying to do matching between device and
  driver
- add wildcard for modpost script
- use unique name for id_table
- move get_mdev_features() to be the first member of virtio_device_ops
  and more comments for it
- typo fixes for the comments above virtio_mdev_ops

Changes from V4:
- keep mdev_set_class() for the device that doesn't use device ops
- use union for device ops pointer in mdev_device
- introduce class specific helper for getting is device ops
- use WARN_ON instead of BUG_ON in mdev_set_virtio_ops
- explain details of get_mdev_features() and get_vendor_id()
- distinguish the optional virito device ops from mandatory ones and
  make get_generation() optional
- rename vfio_mdev.h to vfio_mdev_ops.h, rename virito_mdev.h to
  virtio_mdev_ops.h
- don't abuse version fileds in virtio_mdev structure, use features
  instead
- fix warning during device remove
- style & docs tweaks and typo fixes

Changes from V3:
- document that class id (device ops) must be specified in create()
- add WARN() when trying to set class_id when it has already set
- add WARN() when class_id is not specified in create() and correctly
  return an error in this case
- correct the prototype of mdev_set_class() in the doc
- add documention of mdev_set_class()
- remove the unnecessary "class_id_fail" label when class id is not
  specified in create()
- convert id_table in vfio_mdev to const
- move mdev_set_class and its friends after mdev_uuid()
- suqash the patch of bus uevent into patch of introducing class id
- tweak the words in the docs per Cornelia suggestion
- tie class_id and device ops through class specific initialization
  routine like mdev_set_vfio_ops()
- typos fixes in the docs of virtio-mdev callbacks
- document the usage of virtqueues in struct virtio_mdev_device
- remove the useless vqs array in struct virtio_mdev_device
- rename MDEV_ID_XXX to MDEV_CLASS_ID_XXX

Changes from V2:
- fail when class_id is not specified
- drop the vringh patch
- match the doc to the code
- tweak the commit log
- move device_ops from parent to mdev device
- remove the unused MDEV_ID_VHOST

Changes from V1:
- move virtio_mdev.c to drivers/virtio
- store class_id in mdev_device instead of mdev_parent
- store device_ops in mdev_device instead of mdev_parent
- reorder the patch, vringh fix comes first
- really silent compiling warnings
- really switch to use u16 for class_id
- uevent and modpost support for mdev class_id
- vraious tweaks per comments from Parav

Changes from RFC-V2:
- silent compile warnings on some specific configuration
- use u16 instead u8 for class id
- reseve MDEV_ID_VHOST for future vhost-mdev work
- introduce "virtio" type for mvnet and make "vhost" type for future
  work
- add entries in MAINTAINER
- tweak and typos fixes in commit log

Changes from RFC-V1:
- rename device id to class id
- add docs for class id and device specific ops (device_ops)
- split device_ops into seperate headers
- drop the mdev_set_dma_ops()
- use device_ops to implement the transport API, then it's not a part
  of UAPI any more
- use GFP_ATOMIC in mvnet sample device and other tweaks
- set_vring_base/get_vring_base support for mvnet device


Jason Wang (6):
  mdev: make mdev bus agnostic
  mdev: split out VFIO bus specific parent ops
  mdev: move to drivers/
  mdev: introduce mediated virtio bus
  virtio: introduce a mdev based transport
  docs: sample driver to demonstrate how to implement virtio-mdev
    framework

 .../driver-api/vfio-mediated-device.rst       |  94 ++-
 MAINTAINERS                                   |  11 +-
 drivers/Kconfig                               |   2 +
 drivers/Makefile                              |   1 +
 drivers/gpu/drm/i915/gvt/kvmgt.c              |  26 +-
 drivers/mdev/Kconfig                          |  29 +
 drivers/mdev/Makefile                         |   7 +
 drivers/{vfio => }/mdev/mdev_core.c           | 107 ++-
 drivers/{vfio => }/mdev/mdev_driver.c         |  29 +-
 drivers/{vfio => }/mdev/mdev_private.h        |  18 +-
 drivers/{vfio => }/mdev/mdev_sysfs.c          |   0
 drivers/mdev/vfio.c                           |  76 ++
 drivers/mdev/virtio.c                         | 126 ++++
 drivers/s390/cio/vfio_ccw_ops.c               |  23 +-
 drivers/s390/crypto/vfio_ap_ops.c             |  34 +-
 drivers/s390/crypto/vfio_ap_private.h         |   2 +-
 drivers/vfio/mdev/Kconfig                     |  11 -
 drivers/vfio/mdev/Makefile                    |   4 -
 drivers/vfio/mdev/vfio_mdev.c                 |  45 +-
 drivers/vfio/vfio_iommu_type1.c               |   6 +-
 drivers/virtio/Kconfig                        |  13 +
 drivers/virtio/Makefile                       |   1 +
 drivers/virtio/virtio_mdev.c                  | 409 +++++++++++
 include/linux/mdev.h                          |  49 +-
 include/linux/mdev_vfio.h                     |  68 ++
 include/linux/mdev_virtio.h                   | 168 +++++
 include/linux/mod_devicetable.h               |   8 +
 samples/Kconfig                               |  10 +
 samples/vfio-mdev/Makefile                    |   1 +
 samples/vfio-mdev/mbochs.c                    |  26 +-
 samples/vfio-mdev/mdpy.c                      |  27 +-
 samples/vfio-mdev/mtty.c                      |  24 +-
 samples/vfio-mdev/mvnet_loopback.c            | 690 ++++++++++++++++++
 scripts/mod/devicetable-offsets.c             |   3 +
 scripts/mod/file2alias.c                      |  12 +
 35 files changed, 1924 insertions(+), 236 deletions(-)
 create mode 100644 drivers/mdev/Kconfig
 create mode 100644 drivers/mdev/Makefile
 rename drivers/{vfio => }/mdev/mdev_core.c (76%)
 rename drivers/{vfio => }/mdev/mdev_driver.c (83%)
 rename drivers/{vfio => }/mdev/mdev_private.h (81%)
 rename drivers/{vfio => }/mdev/mdev_sysfs.c (100%)
 create mode 100644 drivers/mdev/vfio.c
 create mode 100644 drivers/mdev/virtio.c
 create mode 100644 drivers/virtio/virtio_mdev.c
 create mode 100644 include/linux/mdev_vfio.h
 create mode 100644 include/linux/mdev_virtio.h
 create mode 100644 samples/vfio-mdev/mvnet_loopback.c
```
#### [PATCH V13 0/6] mdev based hardware virtio offloading support
##### From: Jason Wang <jasowang@redhat.com>

```c
From patchwork Mon Nov 18 10:59:17 2019
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Jason Wang <jasowang@redhat.com>
X-Patchwork-Id: 11249443
Return-Path: <SRS0=wTpQ=ZK=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 624FB13A4
	for <patchwork-kvm@patchwork.kernel.org>;
 Mon, 18 Nov 2019 11:00:15 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id 2FA1020730
	for <patchwork-kvm@patchwork.kernel.org>;
 Mon, 18 Nov 2019 11:00:15 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (1024-bit key) header.d=redhat.com header.i=@redhat.com
 header.b="iJEa69VI"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726614AbfKRLAK (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Mon, 18 Nov 2019 06:00:10 -0500
Received: from us-smtp-delivery-1.mimecast.com ([205.139.110.120]:33557 "EHLO
        us-smtp-1.mimecast.com" rhost-flags-OK-OK-OK-FAIL) by vger.kernel.org
        with ESMTP id S1726490AbfKRLAK (ORCPT <rfc822;kvm@vger.kernel.org>);
        Mon, 18 Nov 2019 06:00:10 -0500
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=redhat.com;
        s=mimecast20190719; t=1574074807;
        h=from:from:reply-to:subject:subject:date:date:message-id:message-id:
         to:to:cc:cc:mime-version:mime-version:content-type:content-type:
         content-transfer-encoding:content-transfer-encoding;
        bh=akgtFB9WKR1gTDoiFAOtLw9d0SMF9qPJ8yqSyvjk4pc=;
        b=iJEa69VIrgU8bDVLO0D8FR0YLsTYwZhyx3NBhvVrIZnoRScoZgBHvWue03U/jOcEyKc+o2
        c2AQvToTDLupW4UJjpoiPiUDYkFYzMsj/Mh8ETwpE4S1XYoC2CwUONQU8492H5K5gLUDOs
        nLCV9GF++cgxA74/1tmn/w7VBwGSchM=
Received: from mimecast-mx01.redhat.com (mimecast-mx01.redhat.com
 [209.132.183.4]) (Using TLS) by relay.mimecast.com with ESMTP id
 us-mta-64-RgIjo4p1OKaXI_lVNhLiyQ-1; Mon, 18 Nov 2019 06:00:03 -0500
Received: from smtp.corp.redhat.com (int-mx02.intmail.prod.int.phx2.redhat.com
 [10.5.11.12])
        (using TLSv1.2 with cipher AECDH-AES256-SHA (256/256 bits))
        (No client certificate requested)
        by mimecast-mx01.redhat.com (Postfix) with ESMTPS id 8D55A1044804;
        Mon, 18 Nov 2019 10:59:58 +0000 (UTC)
Received: from jason-ThinkPad-X1-Carbon-6th.redhat.com
 (ovpn-12-65.pek2.redhat.com [10.72.12.65])
        by smtp.corp.redhat.com (Postfix) with ESMTP id ED3FB60BE1;
        Mon, 18 Nov 2019 10:59:31 +0000 (UTC)
From: Jason Wang <jasowang@redhat.com>
To: kvm@vger.kernel.org, linux-s390@vger.kernel.org,
        linux-kernel@vger.kernel.org, dri-devel@lists.freedesktop.org,
        intel-gfx@lists.freedesktop.org,
        intel-gvt-dev@lists.freedesktop.org, kwankhede@nvidia.com,
        alex.williamson@redhat.com, mst@redhat.com, tiwei.bie@intel.com,
        gregkh@linuxfoundation.org, jgg@mellanox.com
Cc: netdev@vger.kernel.org, cohuck@redhat.com,
        maxime.coquelin@redhat.com, cunming.liang@intel.com,
        zhihong.wang@intel.com, rob.miller@broadcom.com,
        xiao.w.wang@intel.com, haotian.wang@sifive.com,
        zhenyuw@linux.intel.com, zhi.a.wang@intel.com,
        jani.nikula@linux.intel.com, joonas.lahtinen@linux.intel.com,
        rodrigo.vivi@intel.com, airlied@linux.ie, daniel@ffwll.ch,
        farman@linux.ibm.com, pasic@linux.ibm.com, sebott@linux.ibm.com,
        oberpar@linux.ibm.com, heiko.carstens@de.ibm.com,
        gor@linux.ibm.com, borntraeger@de.ibm.com, akrowiak@linux.ibm.com,
        freude@linux.ibm.com, lingshan.zhu@intel.com, eperezma@redhat.com,
        lulu@redhat.com, parav@mellanox.com,
        christophe.de.dinechin@gmail.com, kevin.tian@intel.com,
        stefanha@redhat.com, rdunlap@infradead.org, hch@infradead.org,
        aadam@redhat.com, jakub.kicinski@netronome.com, jiri@mellanox.com,
        jeffrey.t.kirsher@intel.com, Jason Wang <jasowang@redhat.com>
Subject: [PATCH V13 0/6] mdev based hardware virtio offloading support
Date: Mon, 18 Nov 2019 18:59:17 +0800
Message-Id: <20191118105923.7991-1-jasowang@redhat.com>
MIME-Version: 1.0
X-Scanned-By: MIMEDefang 2.79 on 10.5.11.12
X-MC-Unique: RgIjo4p1OKaXI_lVNhLiyQ-1
X-Mimecast-Spam-Score: 0
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Hi all:

There are hardwares that can do virtio datapath offloading while
having its own control path. This path tries to implement a mdev based
unified API to support using kernel virtio driver to drive those
devices. This is done by introducing a new mdev transport for virtio
(virtio_mdev) and register itself as a new kind of mdev driver. Then
it provides a unified way for kernel virtio driver to talk with mdev
device implementation.

Though the series only contains kernel driver support, the goal is to
make the transport generic enough to support userspace drivers. This
means vhost-mdev[1] could be built on top as well by resuing the
transport.

A sample driver is also implemented which simulate a virito-net
loopback ethernet device on top of vringh + workqueue. This could be
used as a reference implementation for real hardware driver.

Also a real IFC VF driver was also posted here[2] which is a good
reference for vendors who is interested in their own virtio datapath
offloading product.

Consider mdev framework only support VFIO device and driver right now,
this series also extend it to support other types. This is done
through decoupling VFIO specific bits out of mdev core and make mdev
an independent module that allows to be used by multiple types of
buses.

Pktgen test was done with virito-net + mvnet loop back device.

Please review.

[1] https://lkml.org/lkml/2019/11/7/62
[2] https://lkml.org/lkml/2019/11/12/215

Changes from V12:
- rebase on top of gvt-linux.git

Changes from V11:
- decouple VFIO specific bits out of mdev core
- make mdev an indepdent module to allow buses other than VFIO mdev
- allow structure composition of mdev through specifiy the size of
  mdev structure
- introduce mdev_vfio structure and store the VFIO specific callbacks
  there
- don't use "mdev" bus for virtio, use a new "mdev_virtio" bus, and
  store the virtio specific callbacks in mdev_virtio structure.
- do the class_id, matching on top of "mdev_virtio" bus

Changes from V10:
- rename mvnet to mvnet_loopback
- fix typo in the help text for sample Kconfig

Changes from V9:
- Tweak the help text for virito-mdev kconfig

Changes from V8:
- try silent checkpatch, some are still there becuase they were inherited
  from virtio_config_ops which needs to be resolved in an independent series
- tweak on the comment and doc
- remove VIRTIO_MDEV_F_VERSION_1 completely
- rename CONFIG_VIRTIO_MDEV_DEVICE to CONFIG_VIRTIO_MDEV

Changes from V7:
- drop {set|get}_mdev_features for virtio
- typo and comment style fixes

Changes from V6:
- rename ops files and compile guard

Changes from V5:
- use dev_warn() instead of WARN(1) when class id is not set
- validate id_table before trying to do matching between device and
  driver
- add wildcard for modpost script
- use unique name for id_table
- move get_mdev_features() to be the first member of virtio_device_ops
  and more comments for it
- typo fixes for the comments above virtio_mdev_ops

Changes from V4:
- keep mdev_set_class() for the device that doesn't use device ops
- use union for device ops pointer in mdev_device
- introduce class specific helper for getting is device ops
- use WARN_ON instead of BUG_ON in mdev_set_virtio_ops
- explain details of get_mdev_features() and get_vendor_id()
- distinguish the optional virito device ops from mandatory ones and
  make get_generation() optional
- rename vfio_mdev.h to vfio_mdev_ops.h, rename virito_mdev.h to
  virtio_mdev_ops.h
- don't abuse version fileds in virtio_mdev structure, use features
  instead
- fix warning during device remove
- style & docs tweaks and typo fixes

Changes from V3:
- document that class id (device ops) must be specified in create()
- add WARN() when trying to set class_id when it has already set
- add WARN() when class_id is not specified in create() and correctly
  return an error in this case
- correct the prototype of mdev_set_class() in the doc
- add documention of mdev_set_class()
- remove the unnecessary "class_id_fail" label when class id is not
  specified in create()
- convert id_table in vfio_mdev to const
- move mdev_set_class and its friends after mdev_uuid()
- suqash the patch of bus uevent into patch of introducing class id
- tweak the words in the docs per Cornelia suggestion
- tie class_id and device ops through class specific initialization
  routine like mdev_set_vfio_ops()
- typos fixes in the docs of virtio-mdev callbacks
- document the usage of virtqueues in struct virtio_mdev_device
- remove the useless vqs array in struct virtio_mdev_device
- rename MDEV_ID_XXX to MDEV_CLASS_ID_XXX

Changes from V2:
- fail when class_id is not specified
- drop the vringh patch
- match the doc to the code
- tweak the commit log
- move device_ops from parent to mdev device
- remove the unused MDEV_ID_VHOST

Changes from V1:
- move virtio_mdev.c to drivers/virtio
- store class_id in mdev_device instead of mdev_parent
- store device_ops in mdev_device instead of mdev_parent
- reorder the patch, vringh fix comes first
- really silent compiling warnings
- really switch to use u16 for class_id
- uevent and modpost support for mdev class_id
- vraious tweaks per comments from Parav

Changes from RFC-V2:
- silent compile warnings on some specific configuration
- use u16 instead u8 for class id
- reseve MDEV_ID_VHOST for future vhost-mdev work
- introduce "virtio" type for mvnet and make "vhost" type for future
  work
- add entries in MAINTAINER
- tweak and typos fixes in commit log

Changes from RFC-V1:
- rename device id to class id
- add docs for class id and device specific ops (device_ops)
- split device_ops into seperate headers
- drop the mdev_set_dma_ops()
- use device_ops to implement the transport API, then it's not a part
  of UAPI any more
- use GFP_ATOMIC in mvnet sample device and other tweaks
- set_vring_base/get_vring_base support for mvnet device

Jason Wang (6):
  mdev: make mdev bus agnostic
  mdev: split out VFIO bus specific parent ops
  mdev: move to drivers/
  mdev: introduce mediated virtio bus
  virtio: introduce a mdev based transport
  docs: sample driver to demonstrate how to implement virtio-mdev
    framework

 .../driver-api/vfio-mediated-device.rst       |  94 ++-
 MAINTAINERS                                   |  11 +-
 drivers/Kconfig                               |   2 +
 drivers/Makefile                              |   1 +
 drivers/gpu/drm/i915/gvt/kvmgt.c              |  24 +-
 drivers/mdev/Kconfig                          |  29 +
 drivers/mdev/Makefile                         |   7 +
 drivers/{vfio => }/mdev/mdev_core.c           | 107 ++-
 drivers/{vfio => }/mdev/mdev_driver.c         |  29 +-
 drivers/{vfio => }/mdev/mdev_private.h        |  18 +-
 drivers/{vfio => }/mdev/mdev_sysfs.c          |   0
 drivers/mdev/vfio.c                           |  76 ++
 drivers/mdev/virtio.c                         | 126 ++++
 drivers/s390/cio/vfio_ccw_ops.c               |  23 +-
 drivers/s390/crypto/vfio_ap_ops.c             |  34 +-
 drivers/s390/crypto/vfio_ap_private.h         |   2 +-
 drivers/vfio/mdev/Kconfig                     |  11 -
 drivers/vfio/mdev/Makefile                    |   4 -
 drivers/vfio/mdev/vfio_mdev.c                 |  45 +-
 drivers/vfio/vfio_iommu_type1.c               |   6 +-
 drivers/virtio/Kconfig                        |  13 +
 drivers/virtio/Makefile                       |   1 +
 drivers/virtio/virtio_mdev.c                  | 409 +++++++++++
 include/linux/mdev.h                          |  49 +-
 include/linux/mdev_vfio.h                     |  68 ++
 include/linux/mdev_virtio.h                   | 168 +++++
 include/linux/mod_devicetable.h               |   8 +
 samples/Kconfig                               |  10 +
 samples/vfio-mdev/Makefile                    |   1 +
 samples/vfio-mdev/mbochs.c                    |  26 +-
 samples/vfio-mdev/mdpy.c                      |  27 +-
 samples/vfio-mdev/mtty.c                      |  24 +-
 samples/vfio-mdev/mvnet_loopback.c            | 690 ++++++++++++++++++
 scripts/mod/devicetable-offsets.c             |   3 +
 scripts/mod/file2alias.c                      |  12 +
 35 files changed, 1923 insertions(+), 235 deletions(-)
 create mode 100644 drivers/mdev/Kconfig
 create mode 100644 drivers/mdev/Makefile
 rename drivers/{vfio => }/mdev/mdev_core.c (76%)
 rename drivers/{vfio => }/mdev/mdev_driver.c (83%)
 rename drivers/{vfio => }/mdev/mdev_private.h (81%)
 rename drivers/{vfio => }/mdev/mdev_sysfs.c (100%)
 create mode 100644 drivers/mdev/vfio.c
 create mode 100644 drivers/mdev/virtio.c
 create mode 100644 drivers/virtio/virtio_mdev.c
 create mode 100644 include/linux/mdev_vfio.h
 create mode 100644 include/linux/mdev_virtio.h
 create mode 100644 samples/vfio-mdev/mvnet_loopback.c
```
#### [PATCH 0/5] KVM: vmx: implement MSR_IA32_TSX_CTRL for guests
##### From: Paolo Bonzini <pbonzini@redhat.com>

```c
From patchwork Mon Nov 18 18:17:42 2019
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Paolo Bonzini <pbonzini@redhat.com>
X-Patchwork-Id: 11250099
Return-Path: <SRS0=wTpQ=ZK=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id DA88F109A
	for <patchwork-kvm@patchwork.kernel.org>;
 Mon, 18 Nov 2019 18:17:57 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id B6CFF222AD
	for <patchwork-kvm@patchwork.kernel.org>;
 Mon, 18 Nov 2019 18:17:57 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=fail reason="signature verification failed" (2048-bit key)
 header.d=gmail.com header.i=@gmail.com header.b="cPW6MV8K"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726647AbfKRSRx (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Mon, 18 Nov 2019 13:17:53 -0500
Received: from mail-wm1-f67.google.com ([209.85.128.67]:37675 "EHLO
        mail-wm1-f67.google.com" rhost-flags-OK-OK-OK-OK) by vger.kernel.org
        with ESMTP id S1726216AbfKRSRx (ORCPT <rfc822;kvm@vger.kernel.org>);
        Mon, 18 Nov 2019 13:17:53 -0500
Received: by mail-wm1-f67.google.com with SMTP id b17so346984wmj.2;
        Mon, 18 Nov 2019 10:17:52 -0800 (PST)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=gmail.com; s=20161025;
        h=sender:from:to:cc:subject:date:message-id;
        bh=UHVmj17X5I97BtGdOpQHVSrZpwkyodA+Uw+toEe9ceM=;
        b=cPW6MV8KJLW7M2YTVyqiYmE2Nl606qjFWgljH4qSErWNWVHim8rZby2mSQpjvc3ST9
         mYGXQ4AKQat8J0GqsiVdm3CJ4SxKB3fBsYMZ1lyX9T9CdckDFD2NV9sDFuHYUjVXilW6
         o08r5tHgtDMdyhQPGJDC5+HJEJWxitIBD4G8RfpS8Cph4v+zScQDfnmJMJJB6uroOwdW
         1Cxc29en4+/8DeLkIMFgFosVz5Ku4E++4Ddr2KE4z7yZrZtV4fCmuqask3S6N1LYVZuY
         d76TWYsDBN5gOsbma+2EZjItqK2aoqdGL4qv7dkmbV8J1RyK0qfK7ezfg+MTMchHYDLU
         OGig==
X-Google-DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=1e100.net; s=20161025;
        h=x-gm-message-state:sender:from:to:cc:subject:date:message-id;
        bh=UHVmj17X5I97BtGdOpQHVSrZpwkyodA+Uw+toEe9ceM=;
        b=YmMhJPEcxIL/L8ArBOmiIDrKPfh5UKU81t4cYxELLeHtqeHXW6WEnxWTwWAl9h2MoX
         Bs8kesOyP/dV9e+hnL6jqvdWdlEROemXluNLa7fTxT6Ufdb1S4fyKRSObMbFhL9IewdT
         wS7h21N28KPeoU8tvToEXKeXD0C5L3K/LVQgtLjGQmHI2JRbGnbAL6Rxobwx2ZxTxLFJ
         W4yT1YCPBmE+ms/8Xfux8RttZ+ymhnao7+e6ZYuaQ+KJjrrG6hvNwPXfgxI00fW+WJ1e
         2/cfc/JUHpKmD5yLjAUacHNygEMYO1Z7JTEh5ntvXxyPz4+Q9Sw4GN5bgCCr0Rwx9Dwh
         q6cw==
X-Gm-Message-State: APjAAAUfXNuHjJ6fwJBKhG3dfo1yzI9rVSZj5hcfTobRwUJNrp5NUlcR
        esBlZd7cHP/iz8M1hwtWK8FpDfuu
X-Google-Smtp-Source: 
 APXvYqzqAAHTKIDQfKYRhTR26Z/txO0yzIIJfqBYR3tKEzIR+8n7zqljh0TP349Ri4tzP/Cy80vtlQ==
X-Received: by 2002:a1c:f317:: with SMTP id q23mr426754wmq.97.1574101071329;
        Mon, 18 Nov 2019 10:17:51 -0800 (PST)
Received: from 640k.localdomain.com ([93.56.166.5])
        by smtp.gmail.com with ESMTPSA id v81sm233794wmg.4.2019.11.18.10.17.50
        (version=TLS1_2 cipher=ECDHE-RSA-AES128-GCM-SHA256 bits=128/128);
        Mon, 18 Nov 2019 10:17:50 -0800 (PST)
From: Paolo Bonzini <pbonzini@redhat.com>
To: linux-kernel@vger.kernel.org, kvm@vger.kernel.org
Cc: jmattson@google.com,
        Sean Christopherson <sean.j.christopherson@intel.com>
Subject: [PATCH 0/5] KVM: vmx: implement MSR_IA32_TSX_CTRL for guests
Date: Mon, 18 Nov 2019 19:17:42 +0100
Message-Id: <1574101067-5638-1-git-send-email-pbonzini@redhat.com>
X-Mailer: git-send-email 1.8.3.1
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

The current guest mitigation of TAA is both too heavy and not really
sufficient.  It is too heavy because it will cause some affected CPUs
(those that have MDS_NO but lack TAA_NO) to fall back to VERW and
get the corresponding slowdown.  It is not really sufficient because
it will cause the MDS_NO bit to disappear upon microcode update, so
that VMs started before the microcode update will not be runnable
anymore afterwards, even with tsx=on.

Instead, if tsx=on on the host, we can emulate MSR_IA32_TSX_CTRL for
the guest and let it run without the VERW mitigation.  Even though
MSR_IA32_TSX_CTRL is quite heavyweight, and we do not want to write
it on every vmentry, we can use the shared MSR functionality because
the host kernel need not protect itself from TSX-based side-channels.

Patch 1 is a minimal fix for MSR_IA32_ARCH_CAPABILITIES for stable
kernels.  The other four patches implement the feature.

Getting some help testing this series with the kvm-unit-tests patch I
have just sent would be great; I could only test this on a machine that
I couldn't reboot, and therefore I could only work on an older kernel.

Paolo Bonzini (5):
  KVM: x86: fix presentation of TSX feature in ARCH_CAPABILITIES
  KVM: x86: do not modify masked bits of shared MSRs
  KVM: x86: implement MSR_IA32_TSX_CTRL effect on CPUID
  KVM: vmx: implement MSR_IA32_TSX_CTRL disable RTM functionality
  KVM: vmx: use MSR_IA32_TSX_CTRL to hard-disable TSX on guest that lack
    it

 arch/x86/include/asm/kvm_host.h |  1 +
 arch/x86/kvm/cpuid.c            |  8 +++--
 arch/x86/kvm/vmx/vmx.c          | 78 ++++++++++++++++++++++++++++++++---------
 arch/x86/kvm/x86.c              | 34 ++++++++----------
 4 files changed, 82 insertions(+), 39 deletions(-)
```
