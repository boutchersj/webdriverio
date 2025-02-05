---
id: protractor-migration
title: कोणमापक से
---

यह ट्यूटोरियल उन लोगों के लिए है जो कोणमापक का उपयोग कर रहे हैं और अपने ढांचे को WebdriverIO में माइग्रेट करना चाहते हैं। यह एंगुलर टीम [द्वारा](https://github.com/angular/protractor/issues/5502) घोषणा के बाद शुरू किया गया था कि प्रोटैक्टर अब समर्थित नहीं होगा। WebdriverIO बहुत सारे प्रोट्रैक्टर डिज़ाइन निर्णयों से प्रभावित रहा है, यही कारण है कि यह संभवतः माइग्रेट करने के लिए निकटतम ढांचा है। WebdriverIO टीम हर एक कोणमापक योगदानकर्ता के काम की सराहना करती है और आशा करती है कि यह ट्यूटोरियल WebdriverIO में संक्रमण को आसान और सीधा बनाता है।

जबकि हम इसके लिए पूरी तरह से स्वचालित प्रक्रिया चाहते हैं, वास्तविकता अलग दिखती है। हर किसी का एक अलग सेटअप होता है और अलग-अलग तरीकों से प्रोटैक्टर का उपयोग करता है। हर कदम को मार्गदर्शन के रूप में देखा जाना चाहिए और कदम दर कदम निर्देश की तरह नहीं। अगर आपको माइग्रेशन से जुड़ी कोई समस्या है, तो बेझिझक [हमसे संपर्क करें](https://github.com/webdriverio/codemod/discussions/new).

## सेटअप

प्रोट्रैक्टर और वेबड्राइवरआईओ एपीआई वास्तव में एक बिंदु पर बहुत समान हैं, जहां अधिकांश कमांड को [कोडमॉड](https://github.com/webdriverio/codemod)के माध्यम से स्वचालित तरीके से फिर से लिखा जा सकता है।

कोडमोड इंस्कोटाल करने के लिए, रन करें:

```sh
npm install jscodeshift @wdio/codemod
```

## रणनीति

कई प्रवासन रणनीतियाँ हैं। आपकी टीम के आकार, परीक्षण फ़ाइलों की मात्रा और माइग्रेट करने की अत्यावश्यकता के आधार पर आप एक बार में सभी परीक्षणों को बदलने या फ़ाइल द्वारा फ़ाइल करने का प्रयास कर सकते हैं। यह देखते हुए कि कोणीय संस्करण 15 (2022 के अंत) तक आपके पास अभी भी पर्याप्त समय है, तब तक कोणमापक को बनाए रखा जाएगा। आप एक ही समय में प्रोटेक्टर और WebdriverIO परीक्षण चला सकते हैं और WebdriverIO में नए परीक्षण लिखना शुरू कर सकते हैं। अपने समय के बजट को देखते हुए आप पहले महत्वपूर्ण परीक्षण मामलों को माइग्रेट करना शुरू कर सकते हैं और उन परीक्षणों पर अपना काम कर सकते हैं जिन्हें आप हटा भी सकते हैं।

## सबसे पहले कॉन्फिग फाइल

कोडमॉड स्थापित करने के बाद हम पहली फ़ाइल को बदलना शुरू कर सकते हैं। पहले [WebdriverIOs कॉन्फ़िगरेशन विकल्प](Configuration.md)में देखें। कॉन्फिग फाइलें बहुत जटिल हो सकती हैं और यह केवल आवश्यक भागों को पोर्ट करने के लिए समझ में आ सकता है और यह देख सकता है कि कुछ विकल्पों को माइग्रेट किए जाने वाले संबंधित परीक्षणों के बाद बाकी को कैसे जोड़ा जा सकता है।

पहले माइग्रेशन के लिए हम केवल कॉन्फिग फाइल को ट्रांसफॉर्म करते हैं और चलाते हैं:

```sh
npx jscodeshift -t ./node_modules/@wdio/codemod/protractor ./conf.ts
```

:::info

 आपके कॉन्फिगर को अलग नाम दिया जा सकता है, हालांकि सिद्धांत समान होना चाहिए: पहले कॉन्फिग को माइग्रेशन करना शुरू करें।

:::

## WebdriverIO निर्भरताएँ स्थापित करें

अगला कदम एक न्यूनतम WebdriverIO सेटअप को कॉन्फ़िगर करना है जिसे हम एक फ्रेमवर्क से दूसरे फ्रेमवर्क में माइग्रेट करते समय बनाना शुरू करते हैं। पहले हम WebdriverIO CLI को इसके माध्यम से स्थापित करते हैं:

```sh
npm install --save-dev @wdio/cli
```

अगला हम कॉन्फ़िगरेशन विज़ार्ड चलाते हैं:

```sh
npx wdio config
```

यह आपको कुछ सवालों से रूबरू कराएगा। इस माइग्रेशन परिदृश्य के लिए आप:
- डिफ़ॉल्ट विकल्प चुनें
- हम अनुशंसा करते हैं कि उदाहरण फ़ाइलें स्वत: जनरेट न करें
- webdriverIO फ़ाइलों के लिए कोई भिन्न फ़ोल्डर चुनें
- और चमेली के ऊपर मोचा चुनने के लिए।

:::जानकारी मोचा ही क्यों?
भले ही आप पहले चमेली के साथ कोणमापक का उपयोग कर रहे हों, मोचा हालांकि बेहतर पुनर्प्रयास तंत्र प्रदान करता है। चुनाव तुम्हारा है!
:::

छोटे प्रश्नपत्र के बाद विज़ार्ड सभी आवश्यक पैकेजों को स्थापित करेगा और उन्हें आपके `package.json`में संग्रहीत करेगा।

## कॉन्फ़िगरेशन फ़ाइल माइग्रेट करें

हमारे पास `conf.ts` और एक नया `wdio.conf.ts`रूपांतरित होने के बाद, अब कॉन्फ़िगरेशन को एक कॉन्फ़िगरेशन से दूसरे में माइग्रेट करने का समय है। केवल पोर्ट कोड सुनिश्चित करें जो सभी परीक्षणों को चलाने में सक्षम होने के लिए आवश्यक है। हमारे में हम हुक फ़ंक्शन और फ्रेमवर्क टाइमआउट को पोर्ट करते हैं।

अब हम अपनी `wdio.conf.ts` फ़ाइल के साथ ही जारी रखेंगे और इसलिए अब मूल कोणमापक कॉन्फ़िगरेशन में किसी भी बदलाव की आवश्यकता नहीं होगी। हम उनको वापस कर सकते हैं ताकि दोनों ढांचे एक-दूसरे के बगल में चल सकें और हम उस समय फाइल पर पोर्ट कर सकें।

## माइग्रेट परीक्षण फ़ाइल

अब हम पहली परीक्षण फ़ाइल को पोर्ट करने के लिए तैयार हैं। सरल शुरुआत करने के लिए, आइए एक से शुरू करें जिसमें तृतीय पक्ष पैकेज या पेजऑब्जेक्ट जैसी अन्य फाइलों पर बहुत अधिक निर्भरता नहीं है। हमारे उदाहरण में माइग्रेट करने वाली पहली फ़ाइल `first-test.spec.ts`है। पहले वह निर्देशिका बनाएँ जहाँ नया WebdriverIO कॉन्फ़िगरेशन इसकी फ़ाइलों की अपेक्षा करता है और फिर इसे ऊपर ले जाएँ:

```sh
mv mkdir -p ./test/specs/
mv test-suites/first-test.spec.ts ./test/specs
```

अब इस फाइल को ट्रांसफॉर्म करते हैं:

```sh
npx jscodeshift -t ./node_modules/@wdio/codemod/protractor ./test/specs/first-test.spec.ts
```

इतना ही! यह फ़ाइल इतनी सरल है कि हमें अब किसी भी अतिरिक्त परिवर्तन की आवश्यकता नहीं है और सीधे WebdriverIO को चलाने का प्रयास कर सकते हैं:

```sh
npx wdio run wdio.conf.ts
```

बधाई हो 🥳 आपने अभी-अभी पहली फ़ाइल माइग्रेट की है!

## अगले चरण

इस बिंदु से आप परीक्षण द्वारा परीक्षण और पृष्ठ वस्तु द्वारा पृष्ठ वस्तु को बदलना जारी रखते हैं। ऐसी संभावना है कि कुछ फाइलों के लिए कोडमोड विफल हो जाएगा जैसे कि त्रुटि:

```
ERR /path/to/project/test/testdata/failing_submit.js Transformation error (Error transforming /test/testdata/failing_submit.js:2)
Error transforming /test/testdata/failing_submit.js:2

> login_form.submit()
  ^

The command "submit" is not supported in WebdriverIO. हम इसके बजाय सबमिट बटन पर क्लिक करने के लिए क्लिक कमांड का उपयोग करने की सलाह देते हैं। इस कॉन्फ़िगरेशन के बारे में अधिक जानकारी के लिए, https://webdriver.io/docs/api/element/click देखें।
  at /path/to/project/test/testdata/failing_submit.js:132:0
```

कुछ कोणमापक आदेशों के लिए WebdriverIO में इसके लिए कोई प्रतिस्थापन नहीं है। इस मामले में कोडमॉड आपको कुछ सलाह देगा कि इसे कैसे रिफैक्टर किया जाए। यदि आप अक्सर ऐसे त्रुटि संदेशों पर ठोकर खाते हैं, तो बेझिझक [एक मुद्दा](https://github.com/webdriverio/codemod/issues/new) उठाएं और एक निश्चित परिवर्तन जोड़ने का अनुरोध करें। जबकि कोडमॉड पहले से ही अधिकांश प्रोटेक्टर एपीआई को बदल देता है, फिर भी सुधार के लिए बहुत जगह है।

## निष्कर्ष

हम आशा करते हैं कि यह ट्यूटोरियल WebdriverIO में माइग्रेशन प्रक्रिया के माध्यम से आपका थोड़ा मार्गदर्शन करेगा। समुदाय विभिन्न संगठनों में विभिन्न टीमों के साथ परीक्षण करते समय कोडमोड में सुधार करना जारी रखता है। कोई मुद्दा उठाने में संकोच न करें [यदि आपके पास फीडबैक है या [चर्चा शुरू](https://github.com/webdriverio/codemod/discussions/new)](https://github.com/webdriverio/codemod/issues/new) यदि आप माइग्रेशन प्रक्रिया के दौरान संघर्ष करते हैं।
