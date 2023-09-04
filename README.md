#React.js ile örnek webpack kullanımı

İlk olarak :

```npm init -y```

komutu ile package.json dosyasını oluşturalım.
Daha sonra oluşturulan bu dosya içerisinde gördüğümüz üzere ana dosya olarak index.js sayfası var. Kök dizinde index.js adında bir dosya oluşturalım.

Şimdi webpack kurulumu ve bunun için gerekli bazı araçların kurulumunu yapacağız.

işte gerekli npm komutları :

```npm i webpack --save-dev```

```npm i webpack-cli --save-dev```

webpack-cli paketi sayasinde webpack komutlarını kullanabiliyor olacağız.

Şimdilik kurduğumuz paketleri ve daha sonra kuracaklarımızı package.json dosyası içinde devDependencies kısmında görebilirsiniz.

Sıradaki işlemimiz şu yine kök dizinde(burası önemli) webpack.config.js adında bir dosya oluşturalım.

```javascript
module.exports = {
    entry: './index.js',
    output: {
        path: __dirname,
        filename:'bundle.js'
    }
}
```

Buradaki entry dosyası adındanda anlaşılacağı üzere giriş dosyası yani bizim webpack'imizin işleyeceği dosya. output ise işlenen dosyanın çıktısı olacak olan dosya. output objesinin içindeki path yolu ifade ediyor ve __dirname webpack.config.js dosyasının bulunduğu klasörü ifade ediyor. filename ise oluşturulacal olan dosyanın adını ifade ediyor.

Gelelim sıradaki işlemimize önce package.json dosyamızı açalım ve ordaki scripts yazan kısımda test adında bir script var onu silelim ve oraya `"dev": "webpack --mode development"` yazalım. Hemen küçük bir deneme yapalım kök dizindeki index.js dosyasını açalım ve console.log("Hello World"); yazalım. ardından komut satırına `npm run dev` yazalım.

Eğer komut satırında "... compiled successfully ..." mesajını gördüyseniz ve dosya dizininde bundle.js adında bir dosya oluştuysa buraya kadar herşey doğru demektir.

Şimdi package.json doyamıza bir script daha ekleyelim.

```"build": "webpack --mode production"```

Bu scripti çalıştırdığınzda bundle.js dosyasının daha az alan kapladığını yani sıkıştırdığını göreceksiniz. Bu script projenin geliştirme evresi bittikten kullanılır.

Şimdide modern projelerdeki dosya dizini yapısına geçelim. Ne demek isteğimi ilerde daha iyi anlayacaksınız.

Kök dizinde dist adında ve src adında iki dosya oluşturalım. src klasörünün içine js adında bir klasör daha oluşturalım ve içine daha önce oluşturduğumuz index.js doyasını taşıyalım. Daha sonra dist klasörünün içine index.html adında bir dosya oluşturalım ve ardından css,img ve js adında 3 farklı dosya oluşturalım. bundle.js dosyasını ise silmenizde fayda var.

Bazı yeni dosyalar oluşturduk ve bazılarının yerlerini değiştirdik bunun üzerine webpack.config.js dosyasında bazı düzenlemeler yapılması lazım.

Güncel webpack.config.js şu şekilde olması lazım :

```javascript
const path = require('path');

module.exports = {
    entry: './src/js/index.js',
    output: {
        path: path.resolve(__dirname,'dist'),
        filename: 'js/bundle.js',
    },
};
```

Dosya yolu değiştiği için `const path = require('path');` diyerek node.js içindeki path modülünü dahil ediyoruz ve yukarıdaki gibi yeni yolu tanıtıyoruz.

Şimdi herşeyi doğru yaptık mı diye bir kontrol sağlayalım ve komut satırına `npm run dev` yazdığınızda ".. compiled successfully ..." cevabını alıyorsanız ve dist>js>bundle.js dosyanız varsa tebrikler buraya kadar herşey doğru.

Buraya kadar yaptıklarrımız sadece webpack konfigürasyonu içindi şimdi devServer paketini kurarak yaptığımız değişiklikleri izleyen ve bunu html dosyasında gösteren paketi kuracağız.

Bunun için gerekli komut :

```npm i webpack-dev-server --save-dev```

Biliyorum sevmeyeceksiniz ama devServer 'i kullanmak için webpack.config.js dosyasını güncellememiz lazım ve güncel hali :

```javascript
const path = require('path');

module.exports = {
    entry: './src/js/index.js',
    output: {
        path: path.resolve(__dirname,'dist'),
        filename: 'js/bundle.js',
    },
    devServer: {
        static:'./dist'
    }
};
```



Projemizi devServer kullanarak çalıştırmak için yeni bir script yazıp package.json dosyasının içine dahil ediyoruz. İşte script :

```"start": "webpack-dev-server --mode development --open"```

localhost:8080'de açılan sayfanın konsol kısmında daha önce yazdığımız "Hello World" yazısını görebiliriz.


Sıra geldi react'ı ve sass'ı dahil etmeye. Bunun için aşağıdaki komutları sırasıyla çalıştırın.

```npm i react react-dom --save```

Eğer react projesini `npx create-react-app` aracılığya kurduysanız package.json dosyasında
`"build": "webpack --config webpack.config.js"` değişikliğini yapın ve dist klasörünün ismini hem dosya dizninde hemde webpack.config.js dosyasında build olarak değiştirin.

React.js'i dahil ettik şimdi sırada babel.js'i yükleyeceğiz.

```npm i @babel/core babel-loader --save-dev```

Üstteki komut temel babel'i yüklerken loader paketi ise js ve jsx uzantılı dosyaları taramak için yüklenir. Alttaki komutta ise preset-env paketi ES6 ve sonrası js kodlarını ES5 kodalrına dönüştürür. preset-react paketi ise react kodalrını js kodlarına dönüştürür.

```npm i @babel/preset-env @babel/preset-react --save-dev```


İsteğe bağlı olarak sass yüklemek için :

```npm i sass sass-loader style-loader css-loader --save-dev```


Bu paketleri yükledikten sonra webpack.config.js dosyasında bazı ayarlamalar yapıldıktan sonraki hali :

```javascript
const path = require('path');

module.exports = {
    entry: './src/js/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'js/bundle.js',
    },
    devServer: {
        static: './dist',
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                },
            },
            {
                test: /\.scss$/,
                use: ['style-loader','css-loader','sass-loader'],
            },
        ],
    },
};

```
Burada test kısmında takip edilecek dosya uzantıları use kısmında ise takip edilen dosyaların dönüşümlerini yapacak paketler yer almaktadır
Sass kullanmak istemiyorsanız 'sass-loader' kısmını çıkarmanız yeterlidir.

Son olarak kök dizine .babelrc adında bir dosya ekleyerek aşağıdaki kod bluğonu yazmak. Bu kod blokları sayesinde babel gerekli ES5 dönüşümlerini yapacak.

Sonra index.html dosyasının body etiketleri arasına `<script src="js/bundle.js"></script>` ve `<div id="root"></div>` yazıyoruz.


Daha sonra index.js dosyasının içini tamizleyip aşağıdaki kodları dahil ediyoruz.

```react.js
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App';

createRoot(document.getElementById('root')).render(<App />);
```

İndex.js içinde yazılan kodların ES5 dönüşümlerini bundle.js içinde gözlemleyebiliriz.

Son olarak :

```npm i babel-polyfill --save```

paketini yükleyerek async-await fonsiyon dönüşümleri sağlanabilir aksi halde asenkron işlemlerde sorun yaşayabiliriz ve webpack.config.js dosyasının nihai hali aşağıdaki gibidir.

``` javascript
const path = require('path');

module.exports = {
    entry: ['babel-polyfill', './src/js/index.js'],
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'js/bundle.js',
    },
    devServer: {
        static: './dist',
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['@babel/preset-react'],
                    },
                },
            },
            {
                test: /\.scss$/,
                use: ['style-loader', 'css-loader', 'sass-loader'],
            },
        ],
    },
};

```

__*NOTE :__
- --save: Bu parametre, yüklenen paketin projenizin çalışma zamanında kullanılan bağımlılıklarını ifade eder. Yani, projenizin çalışması için doğrudan gereken paketlerin listesini oluşturur. Bu paketler, projenizin üretim ortamında da gereklidir. Bu nedenle, projenizi dağıttığınızda bu paketlerin kurulması gerekmektedir.

* Örneğin, npm install react --save komutu, React kütüphanesini projenize ekler ve package.json dosyasının dependencies bölümüne ekler. Bu, projenizin çalışması için gerekli olan bir pakettir.

- --save-dev: Bu parametre, yüklenen paketin yalnızca geliştirme sürecinde kullanılan bağımlılıkları ifade eder. Bu paketler, projenizin geliştirme sürecinde kullanılan araçlar, test kütüphaneleri, derleme veya paketleme yardımcı programları gibi şeyler olabilir. Bu paketler, projenizin üretim ortamında gerekli değildir ve dağıtım sırasında yüklenmesine gerek yoktur.
* Örneğin, npm install webpack --save-dev komutu, Webpack aracını projenize ekler ve package.json dosyasının devDependencies bölümüne ekler. Bu, projenizin geliştirme sürecinde kullanılan bir araçtır ve üretim ortamında gerekli değildir.


* *Author* :  **Ökkeş Akkurt** - [Github](https://github.com/Okkes-Akkurt)
