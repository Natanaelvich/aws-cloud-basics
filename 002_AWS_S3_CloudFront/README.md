# AWS S3 & CloudFront

## Índice

- [O que é AWS S3?](#o-que-é-aws-s3)
- [Conceitos Principais do S3](#conceitos-principais-do-s3)
  - [1. Buckets](#1-buckets)
  - [2. Objetos](#2-objetos)
  - [3. Storage Classes](#3-storage-classes)
  - [4. Versionamento](#4-versionamento)
  - [5. Lifecycle Policies](#5-lifecycle-policies)
  - [6. Bucket Policies](#6-bucket-policies)
- [O que é AWS CloudFront?](#o-que-é-aws-cloudfront)
- [Conceitos Principais do CloudFront](#conceitos-principais-do-cloudfront)
  - [1. Distribuições](#1-distribuições)
  - [2. Origins](#2-origins)
  - [3. Cache Behaviors](#3-cache-behaviors)
  - [4. SSL/TLS Certificates](#4-ssltls-certificates)
- [Integração S3 + CloudFront](#integração-s3--cloudfront)
- [Segurança](#segurança)
- [Melhores Práticas](#melhores-práticas)
- [Casos de Uso Comuns](#casos-de-uso-comuns)
- [Importância para Certificação AWS](#importância-para-certificação-aws)
- [Próximos Passos](#próximos-passos)
- [Links Rápidos do Console AWS](#links-rápidos-do-console-aws)

---

## O que é AWS S3?

Amazon Simple Storage Service (S3) é um serviço de armazenamento de objetos escalável, seguro e durável. É um dos serviços mais fundamentais da AWS, usado para armazenar e recuperar qualquer quantidade de dados a qualquer momento.

### Características Principais:

- **Escalabilidade**: Armazene praticamente qualquer quantidade de dados
- **Durabilidade**: 99.999999999% (11 noves) de durabilidade de objetos
- **Disponibilidade**: 99.99% de disponibilidade
- **Segurança**: Criptografia em trânsito e em repouso
- **Custo-efetivo**: Pay-as-you-go com várias storage classes

**📍 Console AWS:** [Amazon S3](https://s3.console.aws.amazon.com/)

---

## Conceitos Principais do S3

### 1. **Buckets**

Buckets são containers para objetos no S3. Cada bucket tem um nome globalmente único e está associado a uma região AWS.

**Características:**
- Nome deve ser único globalmente (DNS-compliant)
- Cada bucket pertence a uma região específica
- Pode ter configurações de versionamento, logging, encriptação
- Pode ter políticas de acesso (bucket policies)

**📍 Console AWS:** [Gerenciar Buckets](https://s3.console.aws.amazon.com/s3/buckets)

### 2. **Objetos**

Objetos são os arquivos armazenados dentro de buckets. Cada objeto consiste em:
- **Key**: Nome do objeto (similar a um caminho de arquivo)
- **Value**: Dados do objeto (conteúdo do arquivo)
- **Metadata**: Informações sobre o objeto
- **Version ID**: Se versionamento estiver habilitado

**Exemplo de Key:**
```
pasta1/subpasta/arquivo.jpg
```

### 3. **Storage Classes**

S3 oferece diferentes classes de armazenamento otimizadas para diferentes casos de uso:

| Storage Class | Uso Ideal | Durabilidade | Disponibilidade |
|---------------|-----------|--------------|-----------------|
| **S3 Standard** | Dados acessados frequentemente | 99.999999999% | 99.99% |
| **S3 Intelligent-Tiering** | Dados com padrões de acesso desconhecidos | 99.999999999% | 99.9% |
| **S3 Standard-IA** | Dados acessados infrequentemente | 99.999999999% | 99.9% |
| **S3 One Zone-IA** | Dados acessados infrequentemente, não críticos | 99.5% | 99.5% |
| **S3 Glacier Instant Retrieval** | Arquivos grandes acessados ocasionalmente | 99.999999999% | 99.9% |
| **S3 Glacier Flexible Retrieval** | Arquivos acessados 1-2 vezes por ano | 99.999999999% | 99.99% |
| **S3 Glacier Deep Archive** | Arquivos acessados 1-2 vezes por década | 99.999999999% | 99.99% |

**📍 Console AWS:** [Storage Classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html)

### 4. **Versionamento**

Versionamento permite manter múltiplas versões de um objeto no mesmo bucket.

**Benefícios:**
- Recuperar objetos deletados acidentalmente
- Manter histórico de mudanças
- Proteção contra sobrescrita acidental

**📍 Console AWS:** [Configurar Versionamento](https://s3.console.aws.amazon.com/s3/buckets)

### 5. **Lifecycle Policies**

Lifecycle policies automatizam a transição de objetos entre storage classes e a expiração de objetos.

**Exemplos:**
- Mover objetos para Glacier após 90 dias
- Deletar logs após 1 ano
- Transição automática entre tiers

**📍 Console AWS:** [Gerenciar Lifecycle Policies](https://s3.console.aws.amazon.com/s3/buckets)

### 6. **Bucket Policies**

Bucket policies são políticas JSON que controlam o acesso a buckets e objetos.

**Exemplo de Bucket Policy:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::meu-bucket/*"
    }
  ]
}
```

**📍 Console AWS:** [Bucket Policies](https://s3.console.aws.amazon.com/s3/buckets)

---

## O que é AWS CloudFront?

Amazon CloudFront é uma Content Delivery Network (CDN) global que distribui conteúdo estático e dinâmico com baixa latência e alta velocidade de transferência.

### Características Principais:

- **CDN Global**: Mais de 400 pontos de presença (PoPs) em todo o mundo
- **Baixa Latência**: Cache de conteúdo próximo aos usuários finais
- **Segurança**: Suporte a SSL/TLS, WAF integrado
- **Origem Flexível**: S3, EC2, Load Balancers, ou qualquer servidor HTTP/HTTPS
- **Custo-efetivo**: Pagamento apenas pelo que você usa

**📍 Console AWS:** [Amazon CloudFront](https://console.aws.amazon.com/cloudfront/)

---

## Conceitos Principais do CloudFront

### 1. **Distribuições**

Uma distribuição CloudFront é uma configuração que define como o conteúdo será entregue aos usuários.

**Tipos de Distribuições:**
- **Web Distribution**: Para conteúdo HTTP/HTTPS
- **RTMP Distribution**: Para streaming de mídia (legado)

**📍 Console AWS:** [Gerenciar Distribuições](https://console.aws.amazon.com/cloudfront/v3/home#/distributions)

### 2. **Origins**

Origins são os servidores de origem de onde o CloudFront busca o conteúdo.

**Tipos de Origins:**
- **S3 Bucket**: Static website hosting ou bucket S3 padrão
- **EC2 Instance**: Instâncias EC2 com servidores web
- **Load Balancer**: Application ou Classic Load Balancer
- **Custom Origin**: Qualquer servidor HTTP/HTTPS válido

### 3. **Cache Behaviors**

Cache behaviors definem como o CloudFront lida com diferentes tipos de requisições.

**Configurações importantes:**
- **Path Pattern**: Qual caminho (path) usar este comportamento
- **Origin**: Qual origin usar para este path
- **Viewer Protocol Policy**: HTTP, HTTPS, ou Redirect to HTTPS
- **Allowed HTTP Methods**: GET, HEAD, OPTIONS, etc.
- **Cache Policy**: Como cachear o conteúdo

### 4. **SSL/TLS Certificates**

CloudFront suporta certificados SSL/TLS para HTTPS:

- **AWS Certificate Manager (ACM)**: Certificados gerenciados gratuitamente
- **Custom Certificates**: Seus próprios certificados
- **Default CloudFront Certificate**: Certificado wildcard (*.cloudfront.net)

**📍 Console AWS:** [AWS Certificate Manager](https://console.aws.amazon.com/acm/)

---

## Integração S3 + CloudFront

### Configuração Típica:

1. **S3 Bucket** como origin do CloudFront
2. **OAI (Origin Access Identity)** para acesso privado ao S3
3. **CloudFront Distribution** distribuindo conteúdo do S3

**Benefícios:**
- Conteúdo servido via CDN global
- Bucket S3 privado (não acessível diretamente)
- SSL/TLS automático via CloudFront
- Custo reduzido (menos requisições diretas ao S3)

### Origin Access Identity (OAI) / Origin Access Control (OAC):

**OAC (Recomendado):**
- Método moderno de controle de acesso
- Suporta todas as funcionalidades do S3
- Mais simples de configurar

**OAI (Legado):**
- Método anterior ainda suportado
- Limitado em algumas funcionalidades

---

## Segurança

### S3:

1. **Bucket Policies**: Controlar acesso público/privado
2. **ACLs (Access Control Lists)**: Controle fino de acesso
3. **Encryption**: 
   - SSE-S3 (gerenciado pela AWS)
   - SSE-KMS (gerenciado por chaves KMS)
   - SSE-C (chave fornecida pelo cliente)
4. **Block Public Access**: Bloquear acesso público acidental
5. **Versionamento**: Proteção contra sobrescrita/deleção

### CloudFront:

1. **Signed URLs/Cookies**: Distribuir conteúdo privado
2. **WAF Integration**: Proteção contra ataques comuns
3. **SSL/TLS**: Criptografia em trânsito
4. **Origin Access Control**: Prevenir acesso direto ao S3
5. **Geo-restriction**: Restringir acesso por localização geográfica

---

## Melhores Práticas

### S3:

1. **Use nomes de buckets únicos e significativos**
2. **Habilite versionamento para dados críticos**
3. **Configure lifecycle policies para otimizar custos**
4. **Use bucket policies em vez de ACLs quando possível**
5. **Habilite Block Public Access por padrão**
6. **Use SSE-KMS para controle de chaves**
7. **Configure logging (S3 Access Logs)**
8. **Use MFA Delete para dados críticos**

### CloudFront:

1. **Use HTTPS sempre** (Redirect HTTP to HTTPS)
2. **Configure Origin Access Control** para proteger S3
3. **Use Cache Policies** apropriadas para seu conteúdo
4. **Configure custom error pages**
5. **Habilite CloudFront logs** para análise
6. **Use Signed URLs/Cookies** para conteúdo privado
7. **Configure invalidação de cache** quando necessário
8. **Monitore métricas e alarmes**

---

## Casos de Uso Comuns

### S3:

- **Static Website Hosting**: Hospedar sites estáticos
- **Backup e Arquivo**: Armazenar backups e dados de arquivo
- **Data Lake**: Base para data lakes e análises
- **Content Distribution**: Armazenar assets (imagens, vídeos, docs)
- **Application Data**: Armazenar dados de aplicações
- **Disaster Recovery**: Backup para disaster recovery

### CloudFront:

- **Static Website**: Distribuir sites estáticos globalmente
- **API Acceleration**: Acelerar APIs HTTP/HTTPS
- **Video Streaming**: Distribuir vídeos via streaming
- **Software Downloads**: Distribuir software e atualizações
- **Mobile Apps**: Entregar assets para aplicativos móveis
- **Private Content**: Distribuir conteúdo privado via Signed URLs

---

## Importância para Certificação AWS

S3 e CloudFront são fundamentais para o exame AWS Certified Developer – Associate, pois:

- S3 é um dos serviços mais básicos e essenciais da AWS
- CloudFront é essencial para distribuição de conteúdo global
- Questões sobre storage classes, lifecycle policies e cache são frequentes
- Integração entre S3 e CloudFront é comum em cenários do exame
- Bucket policies e segurança são tópicos importantes

---

## Próximos Passos

Após entender os conceitos básicos, pratique:

- Criando buckets S3 com diferentes configurações
- Configurando bucket policies e segurança
- Implementando lifecycle policies
- Criando distribuições CloudFront
- Integrando S3 + CloudFront
- Testando performance e cache

---

## Links Rápidos do Console AWS

### S3

- **Dashboard S3:** [Amazon S3](https://s3.console.aws.amazon.com/)
- **Buckets:** [Gerenciar Buckets](https://s3.console.aws.amazon.com/s3/buckets)
- **Policies:** [Bucket Policies](https://s3.console.aws.amazon.com/s3/buckets)
- **Access Points:** [Gerenciar Access Points](https://s3.console.aws.amazon.com/s3/access-points)

### CloudFront

- **Dashboard CloudFront:** [Amazon CloudFront](https://console.aws.amazon.com/cloudfront/)
- **Distribuições:** [Gerenciar Distribuições](https://console.aws.amazon.com/cloudfront/v3/home#/distributions)
- **Policies:** [Cache Policies](https://console.aws.amazon.com/cloudfront/v3/home#/policies/cache)
- **Invalidations:** [Invalidações de Cache](https://console.aws.amazon.com/cloudfront/v3/home#/invalidations)

### Integração

- **CloudFront Origins:** [Configurar Origins S3](https://console.aws.amazon.com/cloudfront/v3/home#/distributions)
- **OAC/OAI:** [Origin Access Control](https://console.aws.amazon.com/cloudfront/v3/home#/oac)

---

**Recursos Oficiais:**
- [Documentação AWS S3](https://docs.aws.amazon.com/s3/)
- [Documentação AWS CloudFront](https://docs.aws.amazon.com/cloudfront/)
- [S3 User Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/)
- [CloudFront Developer Guide](https://docs.aws.amazon.com/cloudfront/latest/DeveloperGuide/)
