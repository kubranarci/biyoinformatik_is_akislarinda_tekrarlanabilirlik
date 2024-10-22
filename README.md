## **Biyoinformatik iş akışlarının standardizasyonu**

NGS analiz stratejilerinin standardizasyonuna genel bir bakış sunulacak üç saatlik bir oturum planlanmaktadır. Bu oturumda, FAIR ilkelerinin nf-core tabanlı NGS analiz iş akışlarının standardizasyonunu ve uyumlaştırılmasını nasıl mümkün kıldığını keşfedeceğiz. Bu çerçevede nf-core iş akışlarının uyarlanabilirliği ve iş akışlarının standardizasyonunun önemini tartışılaktır. Son olarak, küçük bir genel veri kümesi kullanarak iş akışlarının nasıl ölçeklenebilir, sağlam ve otomatik hale getirilebileceği gösterilecektir.


### Program

|Saat|	Konu|
|:---|:---|
|9:00am - 9:10am|	FAIR data nedir|
|9:10am - 9:30am|	iş akışlarında tekrarlanma, adaptasyon, ve taşınılabilirlik|
|9:30am - 10:15am|	Uygulamalı Kısım 1: Docker, Github, Nextflow ve nf-core araçlarının tanıtımı|
|10:15am - 10:30am|	Ara|
|10:30am - 11:30am|	Uygulamalı Kısım 2: nf-core araçlarını kullanarak basit bir Nextflow iş akışının kurulumu|
|11:30am - 11:50am|	Tekrarlanabilirlik nasıl korunur|
|11:50am - 12:00am|	Özet|

### Eğitim Hedefleri

- FAIR ilkeleri ve bunların iş akışı standardizasyonu ve uyumlaştırma açısından önemi
- nf-core içindeki iş akışlarının uyarlanabilirliği, taşınabilirliği ve ölçeklenebilirliği
- Kodun tekrarlanabilirliğini ve en yüksek kalitesini sağlamak için güçlü iş akışlarının otomasyonu

### Genel Bakış

Aşağıdaki adımları inceleyeceğiz:

1. GitHub kullanarak iş birliği yapmayı öğrenmek.
2. Visual Studio Code ile verimli bir çalışma ortamı oluşturmak.
3. Biyoenformatik araçlari sanal veri ortamlarına taşımak için Docker konteynerlerini inşa edip kullanmak.
4. Nextflow çalıştırmak için bir geliştirme ortamı kurmak.
5. nf-core araçları, iş akışları ve modüllerine genel bakış.
6. nf-core modülleri ve şablonlarını kullanarak bwa-mem ve bcftools ile basit bir varyant çağırma iş akışı oluşturmak.
7. Yapılandırma dosyalarıyla çalışmak ve iş akışını çalıştırmak.
8. Sonuçları incelemek.
9. GitHub Actions kullanarak Sürekli Entegrasyon ortamı uyarlamak.

### Gereksinimler

Lütfen atölye günü için aşağıdaki yazılımları ve kullanıcı hesaplarını hazır bulundurun:


Bilgisayarınızda takip etmek için gereken yazılımlar:

- [GitHub Hesabı](https://github.com/)
- [Docker Hub Hesabı](https://hub.docker.com/signup)
- [Visual Studio Code](https://code.visualstudio.com/) ya da bir diger favori kod editörünüz. 

Hazır bir Nextflow geliştirme ortamı **Gitpod** kullanılarak mevcuttur: Aşamalar:

- Adrese tıklayın: https://gitpod.io/#https://github.com/nextflow-io/training
  -- UYARI: Bu hesap nextflow'a aittir ve ancak egitim sırasinda kullanılabilir https://gitpod.io/#
- GitHub hesabinizla giris yapin (ve gerekli izinleri verin).
- Oturumu acinca GitPod ortami acilmali.
- _Eğer Gitpod ortamını kullanmayı seçerseniz, yerel bilgisayarınıza hiçbir şey yüklemenize gerek kalmaz! Ancak, GitHub ve Docker hesaplarına sahip olmanız gerekecektir_

Atölyeyi bilgisayarınızda takip etmek için aşağıdaki yazılımlara ve dosyalara ihtiyacınız olacak:
- Bash
- [Java11](https://www.oracle.com/java/technologies/downloads/) (ya da ustu,18 e kadar olanlar)
- [Nextflow](https://www.nextflow.io/docs/latest/getstarted.html#installation)
- [nf-core](https://nf-co.re/)
- [nf-core tools on Github](https://github.com/nf-core/tools)
- [Docker](https://www.oracle.com/java/technologies/downloads/)
- [GitHub CLI](https://cli.github.com/)
- [Data](https://github.com/GHGA-Training/gcb_ngs_harmonisation_workshop/tree/main/data) 

## Bölüm 1
### 1. **GitHub** Nedir ve Nasıl Kullanabiliriz?

Yazılımlar için stabil versiyonlar sağlamanın en yaygın yolu git sistemleri kullanmaktır. GitHub, GitLab veya Bitbucket gibi platformlar, yazılım geliştirme sürecinde değerli hizmetler sunar. Bu platformlar, sürüm kontrolü ve iş birliği için kod barındırma/depolama alanı sağlar. Git sistemlerini kullanarak, birbirine bağımlı veya bağımsız farklı dallar (veya kopyalar) oluşturabilirsiniz.

GitHub, iş birliği gerektiren yazılım geliştirme ve sürüm kontrolü için en yaygın kullanılan platformlardan biridir. Bu atölye boyunca aşağıdaki depoları kullanacağız. İsterseniz kubranarci/biyoenformatik_is_akislarinda_tekrarlanabilirlik deposunu fork edebilir veya orijinal belgelerden takip edebilirsiniz.

ya da:

```bash
gh auth login
gh repo fork https://github.com/kubranarci/biyoenformatik_is_akislarinda_tekrarlanabilirlik.git --clone
```

Github deposu fork edildikten sonra kendi hesabinizdan kontrol edilebilir duruma gelecektir.

Github deposu ilk acildiginda 'main' ya da 'master' dallari (branch) uzerinden acilacaktir. Genellikle, ana ortamı (master branch) mümkün olduğunca güvenli tutmak için dallar üzerinde çalışılması önerilir. Bu nedenle, bir _testbranch_ dalı oluşturalım:

Git sistemlerinde bir dal (branch) oluşturmak, proje üzerinde paralel bir çalışma ortamı sağlar ve ana dalın bozulmasını engeller. Oluşturduğunuz yeni dalda kodları test edebilir ve stabil çalıştığından emin olduktan sonra ana dala geri birleştirebilirsiniz (merge).

```bash
git checkout -b testbranch
```

- master dalina gecis yapalim:

```bash
git checkout master
```

- Eger isterseniz dallar su sekilde silinebilir: 

```bash
git checkout -d testbranch
```

Yaptiginiz degsikliklari _push_ etmediginiz surece sizin lokal ortaminizda kalacagini ve yayimlanmayacagini untmayiniz.

- _testbranch_ dalinda yapilan degisiklikleri Github depomuza gonderelim (_push_):

```bash
git push origin testbranch
```

**Alistirma**:

- Simdi, yeni bir dal (_dev_) acip buna gecis yapalim ve bu yeni dal uzerinde calimsmaya baslayalim: .  

> _*Not*: Eger github deposunu kendi hesabiminiza fork etmek istemoyorsaniz, once lokal bilgisayariniza kopyalaiyin ve sonra dilerseniz degisikliklerle birlikte kendi hesabiniza farkli bir isimle depo acarak gonderin!_ 

### 2.  Kendi VS-Code is ortaminizi olusturma:

Birçok iyi düzenleyici mevcut, ancak burada VS-Code kullanacağız. Başka bir tercihiniz varsa, onu da kullanabilirsiniz! Amaç, çoklu hizmetleri bir araya getirip işlevsel bir geliştirme stratejisi oluşturmaktır. Linting, hata ayıklama, düzenleme ve kodu itme işlemleri için bir ortam sağlamaktır. VS-Code'da Nextflow, nf-core tools, GitHub, Python, R gibi birçok eklentiyi ekleyebilirsiniz.

Basitçe, biyoenformatik_is_akislarinda_tekrarlanabilirlik projesini VS-Code'da açabilir ve çalışmaya başlayabilirsiniz!

- VS-code acin

- Eger github deposunu fork etmediyseniz:
    - Basin "Clone Git Repository"
- Ya da:
    - Dopuyu kopyaladiginiz klasorden (lokal) acin

- '_dev_' dalina gecis yapin 
    - Sol alt koseden '_master_' secin
    - dev yazin ve basin.

### 3.  **Docker** konteyneri olusturma:

- Konteynerler, iş akışlarını kendi içinde barındıran ve platformdan bağımsız şekilde dağıtılabilen sanallaştırma teknolojisidir. Bu sayede, paket yöneticileriyle yüklenen yazılımlar, bağımlılıklarıyla birlikte bir image içine paketlenebilir. Bir image, uygulamayı, kodunu ve bağımlılıklarını içeren dosya seti olarak düşünülebilir. Bu imajları kopyalayabilir, kayıt depolarına yükleyebilir, indirebilir ve yeniden kullanabilirsiniz. Biyoinformatikte yaygın olarak kullanılan konteynerleştirme yazılımları arasında Docker, Singularity ve Podman bulunur. 

- Yazılım konteynerlerini kullanmak, iş akışlarımızın taşınabilirliğini sağlamak açısından kritik öneme sahiptir. Bu atölyede, Docker ve Singularity kullanacağız ve derinlemesine incelemek amacıyla kendi bcftools konteynerimizi oluşturacağız. Bu örnek, yazılım bağımlılıklarını yönetmek ve iş akışlarını platformlar arasında sorunsuz bir şekilde taşımak için nasıl bir konteyner oluşturulabileceğini gösterecek. 
  
**Docker**

```bash
docker login
```

- _pull_ komutu bir docker imajini indirmenizi saglar:

```bash
docker pull ubuntu:focal
```

 - Bir konteynerde BASH kabuğu başlatmak, sizi kapsüllenen işletim sisteminde etkileşimli bir modda çalıştırır. Örneğin, bir konteynerde BASH kabuğunu başlatarak dosya sistemi üzerinde gezinebilir, uygulamaları çalıştırabilir, bağımlılıkları test edebilir ve iş akışlarınızı çalıştırabilirsiniz. Bu, kapalı bir ortamda, ana sisteminizi etkilemeden işlemler yapmanıza olanak tanır ve özellikle biyoinformatik iş akışlarını test etmek için faydalıdır.

```bash
docker run -ti ubuntu:focal
```
- Şimdi, ubuntu:focal imajının içinde olacaksınız. Ancak, yerel sisteminizde, kök kullanıcısıyla başlayan root@<containerid> şeklinde bir başka katman oluşturulmuş olmalıdır. Bu, konteynerin kimliğini belirtir ve konteyner içindeki işlemleri yerel sistemden izole bir şekilde yapmanıza olanak tanır. 
  
- Simdi *bcftools* araci icin bir konteynr olusturalim:

```
# Update the package inside the container
apt-get update
# Install the tools we need to download and compile Samtools
apt-get install -y bzip2 g++ libbz2-dev libcurl4-openssl-dev liblzma-dev make ncurses-dev wget zlib1g-dev
# Download Bcftools
wget https://github.com/samtools/bcftools/releases/download/1.17/bcftools-1.17.tar.bz2

# Unpack the archive
tar jxf bcftools-1.17.tar.bz2
cd bcftools-1.17
# Compile the code
make
# Install the resulting binaries
make install
#check if everything is properly installed
bcftools --version
#If complete exit docker
exit
```
root@<containerid> simdi kapandi ve kendi lokal bilgisayariniza geri dondunuz.

- Simdi,  <containerid> i docker hesabiniza kaydedelim (commit).
(Ornek bir imaj ismi su sekilde olabilir: kubran/bcftools:v1.17)
 

```bash
docker commit <containerid> <docker_name>/imagename:version_tag
```

- Commit bittiginde olustirdugunuz imaji Dockerhub a gonderin:

```bash
docker push <docker_name>/imagename:version_tag
```

**Dockerfiles**

- Konteynerleştirmenin tekrarlanabilirliği Dockerfile kullanılarak sağlanabilir. Dockerfile, bir kullanıcının komut satırında çağırabileceği tüm komutları içeren bir metin belgesidir ve bir imaj oluşturur. Manuel konteynerleştirmeye göre büyük bir avantaj sağlar çünkü bu dosya sürümlenebilir, bakımı yapılabilir, düzenlenebilir ve kolayca başkalarıyla paylaşılabilir.

- Şimdi, başka bir imaj oluşturmak için bir Dockerfile kullanalım:
    - Bu Dockerfile dosyasını ortamınıza kaydedin (herhangi bir uzantı kullanmayın ve dosyanın adını Dockerfile olarak belirleyin).


```
# Use an appropriate base image
FROM ubuntu:20.04

# Set the maintainer label
LABEL maintainer="yourname@example.com"

# Install dependencies
RUN apt-get update && apt-get install -y \
    wget \
    bzip2 \
    libbz2-dev \
    zlib1g-dev \
    libncurses5-dev \
    libncursesw5-dev \
    liblzma-dev \
    libcurl4-openssl-dev \
    gcc \
    make \
    && rm -rf /var/lib/apt/lists/*

# Set environment variables
ENV BCFTOOLS_VERSION=1.17

# Download and install BCFtools
RUN wget https://github.com/samtools/bcftools/releases/download/${BCFTOOLS_VERSION}/bcftools-${BCFTOOLS_VERSION}.tar.bz2 && \
    tar -xvjf bcftools-${BCFTOOLS_VERSION}.tar.bz2 && \
    cd bcftools-${BCFTOOLS_VERSION} && \
    make && \
    make install && \
    cd .. && \
    rm -rf bcftools-${BCFTOOLS_VERSION} bcftools-${BCFTOOLS_VERSION}.tar.bz2

```

- Dockerfile ile imaj olusturmak icin asagidaki komutu calistirin:

```bash
docker build --platform linux/amd64 -t bcftools:1.17 .
```

- Imaji Dockerhub icin etiketleyin (tag komutu)

```bash
docker tag  bcftools:1.17 <docker_name>/bcftools:1.17
```

- Imaji Dockerhub`a gonderin (push komutu)

```bash
docker push <docker_name>/bcftools:1.17
```

**Singularity**

- Singularity başka bir konteynerleştirme platformudur ve Docker'a alternatif olarak kullanılabilir. Docker imajları hızla Singularity'e dönüştürülebilir. Ayrıca, Nextflow ile doğrudan Docker'dan dönüştürülmüş Singularity imajlarını kullanmak da mümkündür. Bunu aşağıdaki şekilde yapabilirsiniz:

```
singularity {
    container 'docker://<docker-image>'
}
```
singularity_image = docker://<docker_name>/imagename:version_tag


Bu yöntem, Docker imajlarının Singularity ortamında hızlı ve sorunsuz bir şekilde kullanılmasını sağlar.


### 4. **Nextflow** ve **nf-core** 

Yeni nesil iş akışları, standart analiz bileşenlerini sağlayan iş akışı yönetim sistemleridir. Bu sistemler, şeffaflığı artırır, uzun vadeli sürdürülebilirlik sağlar ve hesaplamalı analizlerin bulunabilir, erişilebilir, birlikte çalışabilir ve yeniden kullanılabilir olmasına yardımcı olur; ayrıca FAIR onaylıdırlar! Snakemake, Galaxy, Cornwell, Pegasus ve Nextflow gibi platformlar açık kaynaklı ve ücretsiz kullanım örnekleridir. İş akışı yöneticileri, yazılımları çalıştırmak için konteynerler kullanarak taşınabilirlik ve istikrar sağlarlar. Ayrıca, otomatik olarak zamanlama yönetimi yaparak mevcut kaynakların etkili kullanımını mümkün kılarlar. Gerekli referans dosyaları bulut kayıt defterlerinden çekerek yerel alan tasarrufu sağlarlar. Ara sonuçları önbelleğe alarak yeniden hesaplamalardan kaçınır ve önemli zaman ve hesaplama kaynaklarından tasarruf sağlarlar. Proje depoları genellikle git sistemlerinde bulunur, bu da iş birliği ve geri bildirim sağlar. Çalışma tamamlandığında, yürütme hakkında ayrıntılı bilgi içeren raporlar oluşturma seçeneği sunarlar.

Nextflow hızla büyüyor ve Seqera Labs tarafından uzun vadeli destek sağlanıyor. 2013'ten beri aynı ekip tarafından geliştirilmiştir. nf-core, Nextflow kullanarak oluşturulmuş küratörlü analiz iş akışlarını toplamak için bir topluluk çabasıdır. Bu çaba yalnızca iş akışları ile sınırlı değildir; ayrıca alt iş akışları ve modüller (tek işlem yapan araçlar) sunarlar. Modüler tasarımı oldukça standarttır ve çok amaçlı kullanıma esneklik sağlar. Ayrıca, şablon iş akışları oluşturmak, modül yüklemek ve test senaryoları oluşturmak için yardımcı araçlar sunarlar.

```bash
nextflow info
```

**nf-core**, Nextflow iş akışlarını optimize etmek ve toplamak için bir topluluk çabasıdır. Şu anda 108 iş akışına sahiptirler! Bu iş akışları, araştırmacılar ve biyoinformatikçiler için standartlaştırılmış ve tekrarlanabilir analiz süreçleri sunarak, veri analizi ve işleme konularında önemli bir kaynak oluşturur. Daha fazla bilgi için nf-core web sitesine göz atabilirsiniz!


```bash
nf-core --help
```

- Kullanilabilir is akislarini listelemek:

```bash
nf-core list
```

nf-core iş akışlarını çalıştırmak oldukça kolaydır! nf-core'un kullandığı kaynakların çoğu bulut ortamında yer almaktadır; bu da, iş akışları ve referans kurulumlarıyla uğraşmanıza gerek kalmayabileceği anlamına gelir. Şimdi, ünlü [rnaseq pipeline](https://nf-co.re/rnaseq/3.14.0) kullanarak bir test çalışması gerçekleştirelim (ya da listelerindeki başka bir iş akışını çalıştırmayı seçebilirsiniz).


- [_test_ profili](https://github.com/nf-core/rnaseq/blob/3.14.0/conf/test.config) icerisinde test komutunu calistirabilmemiz icin yeterli girdi dosyalari ve parametereler mevcuttur.

```bash
# Launch the RNAseq pipeline
nextflow run nf-core/rnaseq -profile test,docker
```

**nf-core** icerisinde is akisini calistiracak moduller (_modules_) bulunmaktadir. nf-core kutuphanesi bu acidan oldukca genistir **1273** den fazla modul barindirir.

- nf-core modullerini listeleyelim:

```bash
nf-core modules list remote
```

## Bölüm 2 

## nf-core araclari kullanarak basit bir hizalama ve varyant cagirma is akisi gelistirilmasi


### 1.  nf-core modulleri kullanilarak is akisini olusturmak

> _*Not*: nf-core araclari kullanilarak nasil is akisi yaratilacagi [burada](https://nf-co.re/docs/tutorials/nextflow_training/creating_with_nf-core)_ detayli bir sekilde aciklanmistir. 

- Simdi biz is akisimizi olustirmak icin nf-core araclarini kullanacagiz. _create_ komutunu calistirarak sablon is akisini elde edelim:

```bash
nf-core create
```

Ben is akisimi isimlendirmek icin "mydemo" kullandim. Eger onceden tanimlanmis parametreleri kabul ederseniz, nf-core otomatik olarak nf-core oneki ekleyecektir. Talimatlari takip ederek _template_ sablon is akisini olusturabilirsiniz .

- Olusturdugumuz klasorun icine gidelim:

```bash
cd nf-core-mydemo
```

- nf-core sablonu tamamen calistirmaya hazir bir sekilde gelmektedir, hic ilerlemeden once isterseniz calistigina bakalim, fakat oncelikli olarak _dev_ branchi olustirarak buda calimsya baslayalim:

```bash
git status
git branch
```

nf-core is akislarinda ozellikle _dev_icerisinde calismaniz ve _TEMPLATE_ brancina dokunmamaniz ogurlenir, bu organizasyon icerisinde daha sonra yapilacak diger degisikleri kopyalayabilmek acisindan onemlidir:


- Şablon, tamamen hazır bir iş akışı oluşturduğundan, aslında bunu çalıştırabiliriz! Bu iş akışı, küçük bir okuma seti içeren basit bir test yapılandırmasıyla birlikte FASTQC ve MultiQC modüllerini içeriyor. Bu, verilerin kalitesini değerlendirmek ve sonuçları görselleştirmek için yararlı bir başlangıç noktası sağlar.

```bash
nextflow run main.nf -profile test,docker --outdir test_results
```

- Simdi sonuclari test_results/ kontrol edelim. 

---

Hadi simdi kendi is akisimizi kurmaya baslayalim:

- _bwa-mem_ kullanarak hizalama yapacagiz ve bunun icin fasta referans genomunun_bwa-index_ kullanilarak indekslenmesi lazim. Sonuc olarak, bizim bwa-mem ve bwa-index modullerine ihtiyacimiz olacak. Sansliyiz ki, nf-core modulleri icerisinde ikisi de mevcut. Simdi  bwa/mem ve bwa/index modulleri kuralim:

```bash
cd nf-core-mydemo
nf-core modules install bwa/mem
nf-core modules install bwa/index
```

Simdi iki modul de  **modules/nf-core/bwa** icerisinde olmalidir. main.nf dosyalarini inceleyelim.

BWA_INDEX modulu fasta dosyasini _tuple_ olarak almali ve cikti olarak da bwa/ dosyasinin icerisinde fasta indekslerini vermeli. Girdi ve cikti dosyalarini ayni _meta_ ile baglayarak birbirlerinin girdi ve ciktilari oldugunu sisteme bildirmis oluyoruz. Bu sayede dosya islemleri karismadan devam edebilir hale gelecektir.

```nextflow
process BWA_INDEX {
    tag "$fasta"
    label 'process_single'

    conda "${moduleDir}/environment.yml"
    container "${ workflow.containerEngine == 'singularity' && !task.ext.singularity_pull_docker_container ?
        'https://depot.galaxyproject.org/singularity/bwa:0.7.18--he4a0461_0' :
        'biocontainers/bwa:0.7.18--he4a0461_0' }"

    input:
    tuple val(meta), path(fasta)

    output:
    tuple val(meta), path(bwa) , emit: index
    path "versions.yml"        , emit: versions

    when:
    task.ext.when == null || task.ext.when

    script:
    def prefix = task.ext.prefix ?: "${fasta.baseName}"
    def args   = task.ext.args ?: ''
    """
    mkdir bwa
    bwa \\
        index \\
        $args \\
        -p bwa/${prefix} \\
        $fasta

    cat <<-END_VERSIONS > versions.yml
    "${task.process}":
        bwa: \$(echo \$(bwa 2>&1) | sed 's/^.*Version: //; s/Contact:.*\$//')
    END_VERSIONS

    """

    stub:
    def prefix = task.ext.prefix ?: "${fasta.baseName}"
    """
    mkdir bwa

    touch bwa/${prefix}.amb
    touch bwa/${prefix}.ann
    touch bwa/${prefix}.bwt
    touch bwa/${prefix}.pac
    touch bwa/${prefix}.sa

    cat <<-END_VERSIONS > versions.yml
    "${task.process}":
        bwa: \$(echo \$(bwa 2>&1) | sed 's/^.*Version: //; s/Contact:.*\$//')
    END_VERSIONS
    """
}

```

- Simdi, bu modulleri is akisimiza eklemek (**include**) icin dosya path`ini kullanacagiz: 

Asagidaki kodu workflows/mydemo.nf dosyasina ekleyin:

```nextflow
/*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    IMPORT MODULES / SUBWORKFLOWS / FUNCTIONS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
*/

...
include { BWA_MEM                } from '../modules/nf-core/bwa/mem/main'
include { BWA_INDEX              } from '../modules/nf-core/bwa/index/main'
...
```

- Hizalama araci olarak _bwa-mem_ kullanabilmek icin, ilk yapmamiz gereken fasta dosyasinin indekslenmesi: 

>_*Not*: Şablon, doğrudan kullanıma hazır bir igenome.config şablonunu içeriyor. Bu nedenle, sağlanan fasta dosyalarından birini doğrudan kullanabiliriz. BWA Index aracı, fasta dosyasını dizinlemek için kullanılacak.  [IGenomes](https://emea.support.illumina.com/sequencing/sequencing_software/igenome.html) AWS tarafından desteklenen referans ve anotasyon koleksiyonları sağlayan bir kaynaktır. Igenome.config, iş akışı için mevcut kaynaklar için parametreler içerir. Daha fazla bilgi için IGenomes sayfasını ziyaret edebilirsiniz._ Şablon, kullanıma hazır bir fasta kanalıyla birlikte geliyor. Lutfen main.nf dosyasina goz atin. Burada _getGenomeAttribute_ isimli fonksiyon fasta kanalini baslatmayi saglayan parametreleri icerir.

- Simdi asagidaki kismi main.nf dosyasinda kesin ve _nextflow.enable.dsl = 2_ yazan satirin altina ekleyin. 

!! Bu yaptigimiz degisiklik sadece sablon uzerinden kaynaklanan hatadan dolayi gerekli!!

```nextflow
nextflow.enable.dsl = 2
include { getGenomeAttribute      } from './subworkflows/local/utils_nfcore_variantbenchmarking_pipeline'
params.fasta = getGenomeAttribute('fasta')

```

- Simdi _fasta_ kanalini ekleyebiliriz.  workflows/mydemo.nf dosyasina asagidaki satirlari ekleyin:

```nextflow

workflow MYDEMO {

    take: 
        samplesheet // channel: samplesheet read in from --input

    main:
...
    // check mandatory parameters
    println(params.fasta)
    ch_fasta       = Channel.fromPath(params.fasta, checkIfExists: true).map{ it -> tuple([id: it[0].getSimpleName()], it) }.collect()

...

}
```

Simdi,

- ch_fasta kanalinin icerisinde ne var bir bakalim, asagidaki satirlari mydemo.nf dosyasina ekleyin ve test is akisimizi calistirin.

```nextflow

ch_fasta.view()
```


```bash
cd ../
nextflow run main.nf -profile test,docker --outdir test_results --fasta "data/ggal_1_48850000_49020000.Ggal71.500bpflank.fa"
```

> _*Not*: Ayni is akisini tekrar tekrar calistirirken isimize en cok yarayan yontemlerden bir tanesi is akisini **-resume** parametresi ile calistirmak oluyor cunku simdiye kadar basarili bir sekilde biten isler bastan baslamak yerine yeniden kullanilabiliyor! Ayrica, eger herhangi bir asamada hata alirsaniz .nextflow.log dosyasina bakmayi unutmayin. Butun ara dosyalar ve modullerin gerceklestirdigi isler _work_ dosyasinda saklaniyor._


- Artık ilk modülümüzü eklemeye hazırız! BWA_INDEX modülünü kontrol ettiğimizde, bwa dizin dizini oluşturabilmek için tek giriş dosyasının bir fasta dosyası olduğunu görüyoruz. Sürece geçmeden önce modüller hakkında birkaç dakika okuyun. Modüller, iş akışınıza belirli işlevsellik ekleyerek analizinizi daha esnek ve özelleştirilebilir hale getirir. Her modül, belirli bir görevi yerine getirmek için tasarlanmış, tekrar kullanılabilir bir yapıdadır[bakiniz](https://www.nextflow.io/docs/latest/module.html). 

Cikti index dosyasi ch_index kanalina kaydedilmistir:. 

```nextflow
    //
    // MODULE: BWA_INDEX
    //
    BWA_INDEX(
        ch_fasta
    )
    ch_index = BWA_INDEX.out.index
    ch_versions = ch_versions.mix(BWA_INDEX.out.versions)
```

Sonucu gormek icin is akisini calistiralim:
```
nextflow run main.nf -profile test,docker --outdir test_results --fasta "data/ggal_1_48850000_49020000.Ggal71.500bpflank.fa" -resume
```

> **Alistirma:** BWA_INDEX cikti dosyasini inceleyelim.   

Simdi BWA_MEM modulunu hizalama islemi icin ekleyebiliriz, fakat ilk once samplesheet.csv icerisine orneklerimizi yukleyelim:

- Bu iş akışı, giriş dosyalarını kontrol etmek ve bunlarla bir giriş kanalı oluşturmak için nf-core alt iş akışını içerir (subworkflows/nf-core/utils_nfvalidation_plugin/main.nf). Bu, başlıkları, örnek adlarını ve örnek dizinlerini otomatik olarak kontrol eder ve doğrular. nf-validation eklentisi, assets/schema_input.json dosyasını kullanarak samplesheet.csv dosyasından CSV başlıklarını otomatik olarak algılar. Projeye özgü bu dosya, analizimizde kullanacağımız formatla aynı olduğu için değiştirmeyeceğiz. [bakiniz](https://nextflow-io.github.io/nf-schema/latest/) about nf-schema structure. 

- Simdi, kendi samplesheet.csv dosyamizi test ornekleri ile dolduralim:
     - mysamplesheet.csv dosyasi olustur ve assets/ altina kaydet: 


```console
sample,fastq_1,fastq_2
gut,data/ggal_gut_1.fq.gz,data/ggal_gut_2.fq.gz
liver,data/ggal_liver_1.fq.gz,data/ggal_liver_2.fq.gz
```

!! dogru dosya pathlerini verdiginizden emin olun


Test çalışmaları yaptığımızda, _test.config_ dosyasını kullanıyorduk. Bu dosya, bu iş akışını çalıştırmak için gereken _--input_ ve _--genome_ parametrelerini tanımlayan hazır bir şablon iş akışına dahildir. Bu yapılandırma dosyası, iş akışını sorunsuz bir şekilde çalıştırmak için gereken tüm parametreleri içerir, bu da süreci kolaylaştırır.

- Simdi de kendi config dosyamiiz olusturmaya baslayim:
    - Bir text dosyasi acin ve asagidakileri icine kopyalarak mytest.config icine kaydedin.


``` nextflow
/*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    Nextflow config file for running minimal tests
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    Defines input files and everything required to run a fast and simple pipeline test.

    Use as follows:
        nextflow run main.nf -profile mytest,<docker/singularity> --outdir <OUTDIR>

----------------------------------------------------------------------------------------
*/

params {
    config_profile_name        = 'Test profile'
    config_profile_description = 'Minimal test dataset to check pipeline function'

    // Limit resources so that this can run on GitHub Actions
    max_cpus   = 2
    max_memory = '6.GB'
    max_time   = '6.h'

    // Input data
    input  = 'assets/mysamplesheet.csv' 

    // Genome references
    //genome = 'hg38'
    fasta = "data/ggal_1_48850000_49020000.Ggal71.500bpflank.fa"
}
```

Simdi  BWA_MEM hizalama modulunu ekleyebiliriz:

BWA_MEM modulu fastq dosyalari ile calisir, bu dosyalari da samplesheet.csv uzerinden ch_samplesheet kanalina okuyoruz. Bir diger gerekli kanal ch_index ise bir once calistirdigimiz _BWA_INDEX_ asamasinda olusmustu.


- BWA_MEM modulunu asagidaki gibi mydemo.nf dosyasina ekleyin : 

```nextflow
    //
    // MODULE: BWA_MEM
    //
    BWA_MEM(
        ch_samplesheet,
        ch_index,
        fasta,
        "true"
    )
    ch_bam = BWA_MEM.out.bam
    ch_versions = ch_versions.mix(BWA_MEM.out.versions)
```

Simdiye kadarki sonuclari gormek icin asagdaki komutu calistirin:

```bash
nextflow run main.nf -profile docker -c conf/mytest.config --outdir test_results -resume
```

> Uyari: Artık test profilini kullanmıyoruz ve kendi yapılandırmamıza geçtik. Bunu nextflow.config dosyasına profil olarak ekleyerek çalıştırabilmemiz için gerekli adımları atmalıyız. Bu, iş akışını yönetmek ve parametreleri düzgün bir şekilde uygulamak için önemlidir. Bu sayede, kendi ayarlarımızla daha esnek bir çalışma ortamı oluşturabiliriz:

```nextflow
    test      { includeConfig 'conf/test.config'      }
    test_full { includeConfig 'conf/test_full.config' }
    mytest    { includeConfig 'conf/mytest.config'    }
```

Son görev, BWA_MEM ile üretilen bam dosyalarını kullanarak varyantları çağırmaktır. Bunun için bcftools mpileup, bcftools call ve bcftools view komutlarını kullanacağız. Oluşturduğumuz bcftools konteynerini kullanarak ilk modülümüzü oluşturalım! Bu, varyant çağırma sürecini otomatikleştirerek analizimizin verimliliğini artıracaktır!

> Tip: Eğer bu görev sizin için fazla zorsa, nf-core'dan hazır bulunan BCFTOOLS_MPILEUP modülünü de kullanabilirsiniz! BWA_INDEX ve BWA_MEM için yaptığımız adımları takip ederek aynı şekilde ilerleyebilirsiniz. Bu, süreçteki karmaşıklığı azaltarak daha hızlı bir çözüm sunacaktır!_


- Taslak BCFTOOLS_MPILEUP modülü şu şekilde görünecek: Varyant çağırma işlemini gerçekleştirecek bcftools komutları ile birlikte giriş ve çıkış dosyalarını tanımlamamız gerekecek. Bu, modülün çalışması için gerekli olan dosya ve komutların doğru bir şekilde entegre edilmesini sağlayacak. Modül, analiz sürecinde varyantları belirlemek için etkili bir araç olacaktır. 

```nextflow
process BCFTOOLS_MPILEUP {
    tag "$meta.id"
    label 'process_medium'

    conda ""
    container "${ workflow.containerEngine == 'singularity' && !task.ext.singularity_pull_docker_container ?
        'singularity_container':
        'docker_container' }"

    input:
    INPUT_FILES

    output:
    OUTPUT_FILES
    path  "versions.yml"                 , emit: versions

    when:
    task.ext.when == null || task.ext.when

    script:
    def args = task.ext.args ?: ''
    def prefix = task.ext.prefix ?: "${meta.id}"
    """
    CMD

    cat <<-END_VERSIONS > versions.yml
    "${task.process}":
        CMD_VERSION
    END_VERSIONS
    """
}

```

bcftools_mpileup.nf dosyasi acin ve modules/local altina kaydedin, alttaki sablon BCFTOOLS_MPILEUP modulunu icerisine kopyalayin. Sirasi ile _INPUT_FILES_, _OUTPUT_FILES_, _CMD_ ve _CMD_VERSION_ kisimlarini ekleyecegiz: 

Ilk olarak bcftools mpilup commandini basitce olusturalim: 

```nextflow
    bcftools \\
        mpileup \\
        --fasta-ref $fasta \\
        -Oz \\
        $bam \\
        | bcftools call --output-type v -mv -Oz \\
        | bcftools view --output-file ${prefix}.vcf.gz --output-type z

    tabix -p vcf -f ${prefix}.vcf.gz
    bcftools stats ${prefix}.vcf.gz > ${prefix}.bcftools_stats.txt

```

bcftools mpileup çalıştırmak için hizalama bam dosyasına ve referans fasta dosyasına ihtiyacımız olacak. Bu işlem, bir indekslenmiş vcf.gz dosyası ve bir txt formatında istatistik dosyası üretecek. Bu nedenle, giriş ve çıkış tanımlamaları şu şekilde olacaktır: 

```nextflow
    input:
    tuple val(meta), path(bam)
    tuple val(meta2), path (fasta)

    output:
    tuple val(meta), path("*vcf.gz")     , emit: vcf
    tuple val(meta), path("*vcf.gz.tbi") , emit: tbi
    tuple val(meta), path("*stats.txt")  , emit: stats
    path  "versions.yml"                 , emit: versions

```

Ayrica bcftools aracinin versiyonunu otomatik olarak kaydedebilmek icin bir yml dosyasi acacagiz:


```nextflow
    cat <<-END_VERSIONS > versions.yml
    "${task.process}":
        bcftools: \$(bcftools --version 2>&1 | head -n1 | sed 's/^.*bcftools //; s/ .*\$//')
    END_VERSIONS
```

 Bu asamada, ya kendi olusturdugumuz bcftools imajini ya da [ biocontainers registry](https://quay.io/repository/biocontainers/bcftools?tab=tags) uzerinden farkli bir bcftools konteyneri bularak kullanabiliriz. 

```nextflow
    conda "bioconda::bcftools=1.17"
    container "${ workflow.containerEngine == 'singularity' && !task.ext.singularity_pull_docker_container ?
        'https://depot.galaxyproject.org/singularity/bcftools:1.17--haef29d1_0':
        'biocontainers/bcftools:1.17--haef29d1_0' }"
```

 Sonucta elde ettigimiz module asagidaki gibi olacaktir:


```nextflow
process BCFTOOLS_MPILEUP {
    tag "$meta.id"
    label 'process_medium'

    conda "bioconda::bcftools=1.17"
    container "${ workflow.containerEngine == 'singularity' && !task.ext.singularity_pull_docker_container ?
        'https://depot.galaxyproject.org/singularity/bcftools:1.17--haef29d1_0':
        'biocontainers/bcftools:1.17--haef29d1_0' }"

    input:
    tuple val(meta), path(bam)
    tuple val(meta2), path (fasta)

    output:
    tuple val(meta), path("*vcf.gz")     , emit: vcf
    tuple val(meta), path("*vcf.gz.tbi") , emit: tbi
    tuple val(meta), path("*stats.txt")  , emit: stats
    path  "versions.yml"                 , emit: versions

    when:
    task.ext.when == null || task.ext.when

    script:
    def prefix = task.ext.prefix ?: "${meta.id}"
    """
    bcftools \\
        mpileup \\
        --fasta-ref $fasta \\
        -Oz \\
        $bam \\
        | bcftools call --output-type v -mv -Oz \\
        | bcftools view --output-file ${prefix}.vcf.gz --output-type z

    tabix -p vcf -f ${prefix}.vcf.gz

    bcftools stats ${prefix}.vcf.gz > ${prefix}.bcftools_stats.txt

    cat <<-END_VERSIONS > versions.yml
    "${task.process}":
        bcftools: \$(bcftools --version 2>&1 | head -n1 | sed 's/^.*bcftools //; s/ .*\$//')
    END_VERSIONS
    """
}
```

- BCFTOOLS_MPILEUP modulunu artik is akisimiza ekleyebiliriz (mydemo.nf):

```nextflow
//
// MODULE: Installed directly locally
//
include { BCFTOOLS_MPILEUP            } from '../modules/local/bcftools_mpileup.nf'

```
  
- Simdi de BCFTOOLS_MPILEUP modulunu ile BWA_MEM modulunun ciktisi ile birlstirelim:

```nextflow
    //
    // MODULE: BCFTOOLS_MPILEUP
    //
    BCFTOOLS_MPILEUP(
        ch_bam,
        ch_fasta
    )
    ch_versions = ch_versions.mix(BCFTOOLS_MPILEUP.out.versions)
```

Dikkat etmemiz gereken diğer bir yapılandırma, konteyner ayarlarına bir ayna ayarlamaktır. Şablon iş akışında varsayılan olarak _quay.io_ kayıt defterleri için bir ayna olarak ayarlanmıştır. Ancak, 3. adımda oluşturduğunuz kendi Docker konteynerinizi kullanmak istiyorsanız, varsayılan ayarları silmeniz gerekir. Bu, konteynerinizin düzgün bir şekilde kullanılmasını sağlayacaktır.

```nextflow
apptainer.registry   = 'quay.io'
docker.registry      = 'quay.io'
podman.registry      = 'quay.io'
singularity.registry = 'quay.io'
```
to 

```nextflow
apptainer.registry   = ''
docker.registry      = ''
podman.registry      = ''
singularity.registry = ''
```

Eger biocontainer imajini kullanmaya karar verdiyseniz sablon ayni kalabilir. 


### 2. Tamamlanmil is akisimizi calistiralim!


Gerçekten de, böyle işlevsel bir iş akışı oluşturmak bu kadar kolay! Adımları takip ederek, gerekli modülleri ve ayarları belirleyerek etkili bir analiz süreci oluşturabilirsiniz. Hazır şablonlar ve modüller sayesinde, karmaşık analizleri daha erişilebilir hale getiriyorsunuz. Kendi konteynerlerinizi kullanarak özelleştirme şansına da sahipsiniz. Tüm süreci daha da verimli kılmak için bu yöntemleri kullanmayı unutmayın!

Burada, oluşturduğumuz yapılandırma dosyası (mytest), hazırladığımız giriş dosyası (mysamplesheet.csv) ve Docker ortamını kullanarak iş akışımızı çalıştırmayı deneyeceğiz. Ancak, bunu Singularity ile de çalıştırabilirsiniz. Ayrıca, çıkış dizinini test_results dışında farklı bir yere tanımlama seçeneğiniz de var. Bu, sonuçlarınızı düzenlemek için esneklik sağlar.

```bash
nextflow run main.nf -profile mytest,docker --outdir test_results -resume

```

İlk çalıştırmada, konteyner görüntüleri ve genomun indirilmesi gerekecek (hepsi otomatik), bu nedenle başlangıçta işlem biraz daha uzun sürecektir!

nf-core araçlarının gerçekten harika bir yanı, projenizi kontrol edebilmesi ve eksik olanları belirtmesidir. Şimdi mevcut iş akışımızı kontrol edelim.

```bash
nf-core lint
```

Bu kod satırı, şablon iş akışıyla ilgili otomatik olarak eklenen TODO'lar hakkında bazı uyarılar yazdıracaktır. Gereksinimleri kendiniz yerine getirmeyi deneyebilirsiniz. Örneğin, iş akışının nasıl çalıştığını açıklayan güzel bir _README.md_ dosyası yazabilir veya _nextflow.config_ ve _nextflow_schema.json_ dosyalarını kullanarak bazı ekstra parametreler ekleyebilirsiniz. Bu, projenizi daha kullanıcı dostu ve kapsamlı hale getirecektir.

### 3. Cikti dosyasini sekillendirme

Eğer test_results dizinini incelediyseniz, otomatik olarak sizin için bwa ve bcftools dizinlerini oluşturduğunu göreceksiniz. Bu dizinler, aslında modül adlarımızın baş harfleridir. Çıktı yapımızı daha iyi şekillendirmek için, modül seçenekleri için argümanları içeren conf/modules.config dosyasını inceleyelim. Bu dosya, her modül için hangi seçeneklerin mevcut olduğunu anlamamıza yardımcı olacaktır.


```nextflow
/*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    Config file for defining DSL2 per module options and publishing paths
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    Available keys to override module options:
        ext.args   = Additional arguments appended to command in module.
        ext.args2  = Second set of arguments appended to command in module (multi-tool modules).
        ext.args3  = Third set of arguments appended to command in module (multi-tool modules).
        ext.prefix = File name prefix for output files.
----------------------------------------------------------------------------------------
*/

process {

    publishDir = [
        path: { "${params.outdir}/${task.process.tokenize(':')[-1].tokenize('_')[0].toLowerCase()}" },
        mode: params.publish_dir_mode,
        saveAs: { filename -> filename.equals('versions.yml') ? null : filename }
    ]

    withName: FASTQC {
        ext.args = '--quiet'
    }

    withName: 'MULTIQC' {
        ext.args   = { params.multiqc_title ? "--title \"$params.multiqc_title\"" : '' }
        publishDir = [
            path: { "${params.outdir}/multiqc" },
            mode: params.publish_dir_mode,
            saveAs: { filename -> filename.equals('versions.yml') ? null : filename }
        ]
    }

}
```

- *BWA_MEM* ve *BCFTOOLS_MPILEUP* sonuclarini daha duzenli gorebilmek icin asagidaki satirlari modules.config dosyasina ekleyelim:

```nextflow
...

    withName: 'BWA_MEM' {
        publishDir = [
            path: { "${params.outdir}/${meta.id}/alignment" },
            pattern: "*{.bam}",
            mode: params.publish_dir_mode
        ]
    }
    withName: 'BCFTOOLS_MPILEUP' {
        publishDir = [
            path: { "${params.outdir}/${meta.id}/variant_calling" },
            pattern: "*{.vcf.gz,tbi,stats}",
            mode: params.publish_dir_mode
        ]
    }    
...
```

netflow config dosyalari hakkinda daha fazla bilgi icin [bakiniz](https://www.nextflow.io/docs/latest/config.html).

### 3. Kaynaklarin optimizasyonu

nf-core modülleri, işlemi çalıştırmak için ortalama miktarda kaynak kullanır. Bu nedenle, araçta ve girdi boyutunda (ortalama boyut) optimize edilmektedir. Bazı modüller, BWA_MEM gibi "process_high" kullanan işlemlerle çok fazla bellek ve CPU gerektirebilir. Küçük bir test örneği çalıştırmak için yüksek işlem gücüne ihtiyacımız yoktur; bu nedenle daha az kaynak kullanabiliriz. Bunu değiştirelim.

 Kaynaklari (cpu=4 ve memory=6GB) mytest.config degistirelim:

```nextflow
process {
   withName:'BWA_MEM'{
        cpus            = { check_max( 2 * task.attempt, 'cpus' ) }
        memory          = { check_max( 6.GB * task.attempt, 'memory' ) }
   }
}
```

> _*Not*: Eger degistirdiginiz deger araciniz icin cok dusuk ise hata alacaksinizdir, fakat  task.attempt sayisinca otomatik olarak komunutuz tekrar gonderilir!_

Kendi is akislarinizi olustururken base.config uzerinden her bir module icin gerekli kaynaklari degistirebilirsiniz.

### 4. Sonuclarin incelenmesi

- _Provenance_, bir hesaplama deneyinin tarihini tanımlar ve iş akışlarının önemli bir yönüdür. Workflow ortamındaki, yazılım sürümlerindeki, parametre ayarındaki ve referans anotasyonlarındaki küçük değişiklikler, her iş akışı çalıştırmasının sonuçlarını değiştirebilir veya değiştirmeyebilir. Ancak, en büyük zorluk, analizlerinizi çalıştırdığınız tam ortamı bilmek ve kaydetmektir. Veri kökeni, bir dizi dosya oluşturmak için kullanılan yöntemlerin, sürümlerin ve argümanların izini tanımlar; yani, analiz için kullandığınız hesaplama ortamının tarihidir

- nf-core pipelines are armed through many provenance tools with the construction of _work_ directory with _temp_ results of each run, saving workflow execution history through .nextflow.log files as well as _CUSTOM_DUMPSOFTWAREVERSIONS_ module and MultiQC tools. 

- nf-core iş akışları, her çalıştırmanın geçici sonuçlarını içeren _work_ dizinini oluşturmak ve _.nextflow.log_ dosyaları aracılığıyla iş akışı yürütme geçmişini kaydetmek için birçok köken araçları ile desteklenmektedir. Ayrıca, _CUSTOM_DUMPSOFTWAREVERSIONS_ modülü ve _MultiQC_ araçları ile birlikte, kullanılan yazılım sürümlerinin kaydedilmesine de olanak tanır. Bu, iş akışlarının izlenebilirliğini ve tekrarlanabilirliğini artırarak daha güvenilir analizler yapılmasını sağlar.

- Kullanılan veriler, üretilen girdi ve çıktılar, oluşturulan belgeler, tüketilen bellek ve CPU gibi yürütme hakkında bilgi, işlenmiş zaman gibi unsurlar, nf-core araçlarının nihai raporlarında kaydedilir. Bu, iş akışlarının verimliliğini artırarak daha iyi sonuçlar elde edilmesini sağlar. 

- MultiQC aracı, analizlerden gelen günlükleri ve raporları bir araya getirir. Analizimizde FASTQC analizi zaten dahil edilmiştir. Bu örnek dosyada, hem FASTQC raporunu hem de yazılım sürümlerini, iş akışının özet bilgisi ile birlikte görebilirsiniz. Bu, analiz sürecinin genel bir görünümünü sağlar ve kullanıcıların yazılım sürümleri hakkında bilgi edinmesine yardımcı olur.

### 5. Degisiklikleri GitHub uzerinden yayinlamak ve Sürekli Entegrasyon (CI) testleri

GitHub kullanmanın bir diğer avantajı kolay hata ayıklamadır. Kodun test edilmesi ve derlenmesi otomatikleştirilebilir. Sürekli Entegrasyon (CI) testleri, yazılım sürüm sürecinin derleme ve birim test aşamalarını ifade eder ve otomatik yazılım geliştirme için önemlidir. Her güncelleme, otomatik bir derleme ve testi tetikler. İncelendiğinde ve onaylandığında, güvenle birleştirilebilir. Hedef, ana dalı hatalardan ve istenmeyen değişikliklerden korumaktır. CI testleri nf-core boru hatları için zorunludur ve örneğimizde nf-core-mydemo, üzerinde çalışabileceğimiz bir ortam sunmaktadır! Otomatik testleri etkinleştirmek için:

- Github uzerinden _actions_ kismini aktive edin (kendi Github reponuz uzerinden nf-core-mydemo)
- .github/workflows/ci.yml dosyasini asagidaki sekilde degistirin:


```yml
    # Only run on push if this is the nf-core dev branch (merged PRs)
    if: "${{ github.event_name != 'push' || (github.event_name == 'push' && github.repository == 'nf-core/mydemo') }}"
```
-> 

```yml
    # Only run on push
    if: "${{ github.event_name != 'push' || (github.event_name == 'push' && github.repository == '<your_github_name>/nf-core-mydemo') }}"
```

Kendi test profilimizi calistiracagimiz icin bunu da degistirmeliyiz:

```yml
      - name: Run pipeline with test data
        # TODO nf-core: You can customise CI pipeline run tests as required
        # For example: adding multiple test runs with different parameters
        # Remember that you can parallelise this by using strategy.matrix
        run: |
          nextflow run ${GITHUB_WORKSPACE} -profile test,docker --outdir ./results
```
-->  

```yml
      - name: Run pipeline with test data
        # TODO nf-core: You can customise CI pipeline run tests as required
        # For example: adding multiple test runs with different parameters
        # Remember that you can parallelise this by using strategy.matrix
        run: |
          nextflow run ${GITHUB_WORKSPACE} -profile mytest,docker --outdir ./results
```

Artık dev dalına değişiklikler yaptığımızda, iş akışının/kodun sorunsuz çalışıp çalışmadığını kontrol etmek için küçük bir test boru hattı tetiklenecek.
    
Son yapmamız gereken, depomuzdaki değişiklikleri kaydetmek ve üzerinde çalıştığımız dev dalına göndermektir.

```bash
git add .
git commit -a -m "commit" (commit message)
```

Değişiklikleri, eğer GitHub uzantılarını kurduysanız, VS Code kullanarak da kaydedebilirsiniz. Değişiklikler, çalışma alanının sol tarafında görüntülenecektir.

Tamamladığınızda, GitHub deposunun üst kısmındaki _Actions_ bölümünden testi izleyebilirsiniz. Her şey yolunda giderse, test hatasız bir şekilde tamamlanacaktır!


> **Son NOT**: https://github.com/kubranarci/nf-core-mydemo/tree/dev simdiye kadar yapmaya calistigimiz is akisini bulabilirsiniz.

### Kaynaklar

nextflow ve nf-core:
- Nextflow homepage: https://www.nextflow.io/
- Nextflow training material: https://training.nextflow.io
- Pipeline examples: https://www.nextflow.io/example1.html
- Main documentation: https://www.nextflow.io/docs/latest/index.html
- Common implementation patterns for developers: http://nextflow-io.github.io/patterns/index.html

Docker:
- https://docker-curriculum.com/
  
Github Actions & GitHub CI:
- https://docs.github.com/en/actions/automating-builds-and-tests/about-continuous-integration
- https://resources.github.com/ci-cd/


Sorular icin kuebra.narci@dkfz-heidelberg.de adresine mail atabilirsiniz. 