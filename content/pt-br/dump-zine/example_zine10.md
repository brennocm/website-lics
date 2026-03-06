---
title: "0x09_Redes_Neurais_e_Segurança"
author: "Brenno Miranda"
edition: "dmp 0x3"
translationKey: "zine_ml_security"
---

## --[ Sumário
- 0 -> [IA na Trincheira]
- 1 -> [Modelos Adversariais]
- 2 -> [Detecção de Malware com ML]
- 3 -> [PoC: Classificador de Tráfego Malicioso]
- 4 -> [Encerramento e Créditos]

### 0 - IA na Trincheira

E aí, galera!

Machine Learning não é mais buzzword de startup — chegou com tudo na segurança ofensiva e defensiva. Do lado defensivo, temos EDRs usando classificadores para detectar malware que AV tradicional não pega. Do lado ofensivo, temos adversarial ML para contornar essas detecções.

A interseção entre IA e hacking é um dos campos mais promissores e perigosos da computação moderna. E como toda ferramenta, pode ser usada para atacar ou defender.

### 1 - Modelos Adversariais

A ideia central de adversarial ML é manipular o input de um modelo para que ele classifique incorretamente. No contexto de segurança, isso significa criar malware que um classificador ML identifica como benigno.

### 2 - Detecção de Malware com ML

Vamos construir um classificador básico que analisa features extraídas de binários PE:

```python
#!/usr/bin/env python3
import numpy as np
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

# Features extraídas de binários PE:
# [tamanho, num_sections, entropy, num_imports, has_debug, packed]
X = np.array([
    [45056, 3, 5.2, 12, 1, 0],   # benigno
    [12288, 1, 7.8, 3, 0, 1],    # malware
    [98304, 5, 4.9, 45, 1, 0],   # benigno
    [8192, 2, 7.9, 2, 0, 1],     # malware
    [204800, 4, 5.1, 67, 1, 0],  # benigno
    [16384, 1, 7.6, 5, 0, 1],    # malware
])
y = np.array([0, 1, 0, 1, 0, 1])

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3)

clf = RandomForestClassifier(n_estimators=100, random_state=42)
clf.fit(X_train, y_train)

predictions = clf.predict(X_test)
print(classification_report(y_test, predictions, target_names=["benigno", "malware"]))

# Feature importance
features = ["tamanho", "sections", "entropia", "imports", "debug", "packed"]
for name, importance in zip(features, clf.feature_importances_):
    print(f"  {name}: {importance:.3f}")
```

A entropia alta e número baixo de imports são os maiores indicadores de binários empacotados/ofuscados — que é um padrão típico de malware.

_Figura 1. Classificador RandomForest para detecção de malware._
