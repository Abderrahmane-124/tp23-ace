# TP 23 - Migration de Eureka vers Consul

## 🎯 Objectifs

Ce TP démontre la migration d'une architecture microservices de **Netflix Eureka** vers **HashiCorp Consul** pour la découverte de services.


---

## 🚀 Prérequis

- **Java 17+**
- **Maven 3.6+**
- **Consul** (télécharger depuis [consul.io/downloads](https://www.consul.io/downloads))

---

## 📝 Installation de Consul

1. Télécharger Consul depuis https://www.consul.io/downloads
2. Extraire vers `C:\Consul` (Windows)
3. Ajouter au PATH système
4. Vérifier l'installation :
   ```bash
   consul --version
   ```

---

## ▶️ Démarrage

### 1. Lancer Consul (mode développement)

```bash
consul agent -dev
```

Interface Web : http://localhost:8500/

### 2. Démarrer les microservices

Ouvrir 3 terminaux séparés :

**Terminal 1 - Gateway :**
```bash
cd ms_rest_template/gateway
mvn spring-boot:run
```

**Terminal 2 - Service Client :**
```bash
cd ms_rest_template/client
mvn spring-boot:run
```

**Terminal 3 - Service Car :**
```bash
cd ms_rest_template/car
mvn spring-boot:run
```

---

## ✅ Vérification

### Consul UI
Ouvrir http://localhost:8500/ui et vérifier que les 3 services sont enregistrés :
- `Gateway`
- `SERVICE-CLIENT`
- `SERVICE-CAR`

### Endpoints de test

| Service | Accès Direct | Via Gateway |
|---------|--------------|-------------|
| Client  | http://localhost:8088/clients | http://localhost:8888/service-client/clients |
| Car     | http://localhost:8082/cars | http://localhost:8888/service-car/cars |

### Console H2
- Client : http://localhost:8088/h2-console (JDBC URL: `jdbc:h2:mem:clientdb`)
- Car : http://localhost:8082/h2-console (JDBC URL: `jdbc:h2:mem:cardb`)

---

## 🔄 Modifications effectuées (Migration)

### Pour chaque service (client, car, gateway) :

1. **pom.xml** : Remplacement de la dépendance
   ```xml
   <!-- AVANT -->
   <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
   
   <!-- APRÈS -->
   <artifactId>spring-cloud-starter-consul-discovery</artifactId>
   ```

2. **application.yml** : Configuration Consul
   ```yaml
   spring:
     cloud:
       consul:
         host: localhost
         port: 8500
         discovery:
           service-name: SERVICE-NAME
   ```

3. **Application.java** : Ajout de l'annotation
   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class ServiceApplication { ... }
   ```

---

## 📁 Structure du projet

```
ms_rest_template/
├── client/          # Service Client (Port 8088)
├── car/             # Service Car (Port 8082)
├── gateway/         # API Gateway (Port 8888)
└── server_eureka/   # (Non utilisé après migration)
```

---

