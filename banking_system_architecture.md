# VisaNetPay - Sistema de Arquitetura Avançado para Ecossistema Fechado de Pagamentos

## Visão Geral da Arquitetura

O VisaNetPay é um ecossistema fechado de pagamentos que opera com ledger interno próprio, suportando múltiplas moedas Fiat e Criptomoedas. O sistema não depende de fontes externas para transações entre membros, garantindo autonomia completa e controle total sobre as operações financeiras.

## Abordagem de Implementação

### Pontos Críticos Identificados

1. **Gerenciamento de Chaves Privadas**: Implementação obrigatória de HSM (Hardware Security Module) para custódia segura de chaves de criptomoedas
2. **Transações Atômicas**: Garantia de consistência ACID em todas as operações de saldo para prevenir condições de corrida
3. **Ledger Imutável**: Implementação de estrutura append-only para auditoria completa e rastreabilidade
4. **Multi-Currency Precision**: Suporte a diferentes precisões decimais (2 para Fiat, 8 para Cripto)
5. **Geração de ID Único**: Algoritmo não-sequencial para member_id de 8 dígitos

### Framework e Tecnologias Selecionadas

**Frontend Stack:**
- Next.js 14 com App Router para performance otimizada
- TypeScript para type safety em operações financeiras
- Shadcn-ui para componentes de interface consistentes
- Tailwind CSS para estilização responsiva
- Zustand para gerenciamento de estado das carteiras

**Backend Stack:**
- Supabase PostgreSQL para banco de dados principal
- Supabase Edge Functions para lógica de negócio
- Supabase Realtime para atualizações em tempo real
- AWS KMS/HSM para gerenciamento de chaves privadas
- Node.js crypto library para operações criptográficas

**Bibliotecas Específicas:**
- `qrcode` para geração de QR Codes
- `jsPDF` para geração de comprovantes PDF
- `bitcoinjs-lib` para operações Bitcoin
- `web3` para operações USDT/Ethereum
- `bcrypt` para hashing de dados sensíveis

## Estruturas de Dados e Interfaces

### Modelos de Domínio Core

```typescript
// Interfaces TypeScript para o sistema

interface User {
  id: string;
  member_id: string; // 8 dígitos únicos
  email: string;
  name: string;
  role: 'admin' | 'manager' | 'operator' | 'user';
  permissions: Record<string, boolean>;
  status: 'active' | 'inactive' | 'suspended';
  created_at: Date;
  updated_at: Date;
}

interface Wallet {
  id: string;
  user_id: string;
  created_at: Date;
}

interface Balance {
  id: string;
  wallet_id: string;
  asset_code: 'USD' | 'EUR' | 'BRL' | 'GBP' | 'BTC' | 'USDT_TRC20';
  amount: Decimal; // Precisão variável por tipo
  locked_amount: Decimal; // Para transações pendentes
  updated_at: Date;
}

interface CryptoAddress {
  id: string;
  user_id: string;
  asset_code: 'BTC' | 'USDT_TRC20';
  address: string; // Endereço público
  private_key_reference: string; // Referência HSM
  is_active: boolean;
  created_at: Date;
}

interface InternalTransfer {
  id: string;
  transaction_uuid: string;
  payer_wallet_id: string;
  payee_wallet_id: string;
  asset_code: string;
  amount: Decimal;
  status: 'COMPLETED' | 'FAILED';
  related_payment_request_id?: string;
  description?: string;
  metadata: Record<string, any>;
  created_at: Date;
}

interface PaymentRequest {
  id: string;
  payee_user_id: string;
  amount: Decimal;
  asset_code: string;
  description?: string;
  status: 'PENDING' | 'PAID' | 'EXPIRED' | 'CANCELLED';
  payment_link: string;
  qr_code_data: string; // Base64
  expires_at?: Date;
  paid_at?: Date;
  paid_by_user_id?: string;
  created_at: Date;
}

interface CryptoDeposit {
  id: string;
  user_id: string;
  crypto_address_id: string;
  asset_code: 'BTC' | 'USDT_TRC20';
  amount: Decimal;
  transaction_hash: string;
  block_height?: number;
  confirmations: number;
  status: 'PENDING' | 'CONFIRMED' | 'FAILED';
  processed_at?: Date;
  created_at: Date;
}

interface Receipt {
  id: string;
  transfer_id: string;
  pdf_path: string;
  pdf_data: Buffer;
  generated_at: Date;
}
```

### Serviços e APIs

```typescript
// Serviços principais do sistema

class UserService {
  async createUser(userData: CreateUserRequest): Promise<User>;
  async generateMemberId(): Promise<string>;
  async findByMemberId(memberId: string): Promise<User | null>;
  async updatePermissions(userId: string, permissions: Record<string, boolean>): Promise<void>;
}

class WalletService {
  async createWallet(userId: string): Promise<Wallet>;
  async initializeBalances(walletId: string): Promise<Balance[]>;
  async getBalance(walletId: string, assetCode: string): Promise<Decimal>;
  async lockAmount(walletId: string, assetCode: string, amount: Decimal): Promise<void>;
  async unlockAmount(walletId: string, assetCode: string, amount: Decimal): Promise<void>;
}

class CryptoService {
  async generateAddress(userId: string, assetCode: 'BTC' | 'USDT_TRC20'): Promise<CryptoAddress>;
  async storePrivateKeyReference(address: string, keyReference: string): Promise<void>;
  async signTransaction(keyReference: string, transactionData: any): Promise<string>;
  async monitorDeposits(): Promise<void>;
}

class TransferService {
  async executeInternalTransfer(request: TransferRequest): Promise<InternalTransfer>;
  async validateSufficientBalance(walletId: string, assetCode: string, amount: Decimal): Promise<boolean>;
  async processAtomicTransfer(
    payerWalletId: string, 
    payeeWalletId: string, 
    assetCode: string, 
    amount: Decimal
  ): Promise<InternalTransfer>;
}

class PaymentRequestService {
  async createPaymentRequest(request: CreatePaymentRequest): Promise<PaymentRequest>;
  async generateQRCode(paymentData: any): Promise<string>;
  async generatePaymentLink(requestId: string): Promise<string>;
  async processPayment(requestId: string, payerUserId: string): Promise<InternalTransfer>;
}

class ReceiptService {
  async generatePDF(transferId: string): Promise<Receipt>;
  async customizePDFTemplate(template: PDFTemplate): Promise<void>;
  async sendReceiptByEmail(receiptId: string, email: string): Promise<void>;
}

class HSMService {
  async generateKeyPair(assetCode: string): Promise<{ address: string; keyReference: string }>;
  async signWithHSM(keyReference: string, data: Buffer): Promise<string>;
  async validateSignature(publicKey: string, signature: string, data: Buffer): Promise<boolean>;
}

class AuditService {
  async logTransaction(transaction: InternalTransfer): Promise<void>;
  async generateAuditReport(dateRange: DateRange): Promise<AuditReport>;
  async validateLedgerIntegrity(): Promise<boolean>;
}
```

### APIs REST

```typescript
// Endpoints da API REST

// Autenticação e Usuários
POST   /api/auth/login
POST   /api/auth/register
GET    /api/users/profile
PUT    /api/users/profile
GET    /api/users/by-member-id/{memberId}

// Carteiras e Saldos
GET    /api/wallets/balances
GET    /api/wallets/balances/{assetCode}
POST   /api/wallets/addresses/generate
GET    /api/wallets/addresses

// Transferências Internas
POST   /api/transfers/internal
GET    /api/transfers/history
GET    /api/transfers/{transferId}
POST   /api/transfers/validate

// Sistema de Cobrança
POST   /api/payment-requests
GET    /api/payment-requests
GET    /api/payment-requests/{requestId}
POST   /api/payment-requests/{requestId}/pay
PUT    /api/payment-requests/{requestId}/cancel

// Depósitos Cripto
GET    /api/crypto/deposits
POST   /api/crypto/deposits/notify
GET    /api/crypto/deposits/{depositId}/status

// Comprovantes
GET    /api/receipts/{transferId}
POST   /api/receipts/{transferId}/email
GET    /api/receipts/{transferId}/pdf

// Administração
GET    /api/admin/system-status
GET    /api/admin/audit-reports
POST   /api/admin/users/{userId}/suspend
PUT    /api/admin/system-settings
```

## Fluxo de Chamadas do Programa

O sistema segue fluxos bem definidos para cada operação principal:

### Fluxo de Registro de Usuário

```
Cliente -> AuthController.register()
AuthController -> UserService.createUser()
UserService -> UserService.generateMemberId()
UserService -> Database.createUser()
UserService -> WalletService.createWallet()
WalletService -> WalletService.initializeBalances()
WalletService -> CryptoService.generateAddress('BTC')
CryptoService -> HSMService.generateKeyPair()
HSMService -> Database.storeCryptoAddress()
WalletService -> CryptoService.generateAddress('USDT_TRC20')
CryptoService -> HSMService.generateKeyPair()
HSMService -> Database.storeCryptoAddress()
WalletService -> Database.createBalances()
AuthController -> Cliente (User + Wallet criados)
```

### Fluxo de Transferência Interna

```
Cliente -> TransferController.executeTransfer()
TransferController -> UserService.findByMemberId()
TransferController -> TransferService.validateSufficientBalance()
TransferService -> WalletService.getBalance()
TransferService -> Database.beginTransaction()
TransferService -> WalletService.lockAmount(payer)
WalletService -> Database.updateBalance(payer, -amount)
WalletService -> Database.updateBalance(payee, +amount)
TransferService -> Database.createInternalTransfer()
TransferService -> AuditService.logTransaction()
TransferService -> Database.commitTransaction()
TransferService -> ReceiptService.generatePDF()
ReceiptService -> Database.createReceipt()
TransferController -> NotificationService.notifyUsers()
TransferController -> Cliente (Transfer completo)
```

### Fluxo de Geração de QR Code

```
Cliente -> PaymentController.createRequest()
PaymentController -> PaymentRequestService.createPaymentRequest()
PaymentRequestService -> Database.createPaymentRequest()
PaymentRequestService -> PaymentRequestService.generatePaymentLink()
PaymentRequestService -> PaymentRequestService.generateQRCode()
PaymentRequestService -> Database.updatePaymentRequest()
PaymentController -> Cliente (Link + QR Code)
```

### Fluxo de Pagamento via QR/Link

```
Cliente -> PaymentController.processPayment()
PaymentController -> PaymentRequestService.validateRequest()
PaymentRequestService -> Database.getPaymentRequest()
PaymentController -> TransferService.executeInternalTransfer()
TransferService -> [Fluxo de Transferência Interna]
TransferService -> Database.updatePaymentRequestStatus('PAID')
PaymentController -> NotificationService.notifyPayee()
PaymentController -> Cliente (Pagamento processado)
```

### Fluxo de Depósito Cripto

```
BlockchainMonitor -> CryptoService.monitorDeposits()
CryptoService -> BlockchainAPI.getTransactions()
CryptoService -> Database.findAddressByAddress()
CryptoService -> Database.createCryptoDeposit()
CryptoService -> CryptoService.waitForConfirmations()
CryptoService -> WalletService.creditBalance()
WalletService -> Database.updateBalance()
CryptoService -> Database.updateDepositStatus('CONFIRMED')
CryptoService -> NotificationService.notifyUser()
```

## Considerações de Segurança

### Gerenciamento de Chaves Privadas

- **HSM Integration**: Todas as chaves privadas são geradas e armazenadas em Hardware Security Module
- **Referências Seguras**: Aplicação armazena apenas referências criptografadas, nunca chaves completas
- **Rotação de Chaves**: Processo automatizado de rotação periódica de chaves
- **Backup Distribuído**: Backup das referências em múltiplas localizações geográficas

### Transações Atômicas

- **ACID Compliance**: Todas as operações de saldo seguem propriedades ACID
- **Rollback Automático**: Reversão imediata em caso de falha em qualquer etapa
- **Lock Otimista**: Prevenção de condições de corrida em operações concorrentes
- **Idempotência**: Garantia de que operações duplicadas não causam efeitos colaterais

### Auditoria e Compliance

- **Ledger Imutável**: Registro append-only de todas as transações
- **Hash Chain**: Cada transação contém hash da anterior para integridade
- **Assinaturas Digitais**: Validação criptográfica de cada operação
- **Logs Detalhados**: Registro completo de todas as ações administrativas

### Validação e Sanitização

- **Input Validation**: Validação rigorosa de todos os dados de entrada
- **Rate Limiting**: Limitação de tentativas por usuário e IP
- **Sanitização SQL**: Prevenção de SQL injection em todas as queries
- **Encryption at Rest**: Criptografia de dados sensíveis em repouso

## Monitoramento e Observabilidade

### Métricas de Sistema

- **Performance**: Tempo de resposta de APIs e processamento de transações
- **Disponibilidade**: Uptime de serviços críticos e dependências
- **Segurança**: Tentativas de acesso não autorizado e anomalias
- **Negócio**: Volume de transações, saldos totais por moeda, usuários ativos

### Alertas Automáticos

- **Transações de Alto Valor**: Alert para transações acima de limites configuráveis
- **Falhas de Sistema**: Notificação imediata para erros críticos
- **Anomalias de Segurança**: Detecção de padrões suspeitos
- **Capacidade**: Alertas de utilização de recursos próximos aos limites

### Dashboards de Monitoramento

- **Operacional**: Status em tempo real de todos os serviços
- **Financeiro**: Visualização de fluxos e saldos por moeda
- **Segurança**: Painel de eventos de segurança e auditoria
- **Performance**: Métricas de latência e throughput

## Aspectos Não Claros

### Regulamentação e Compliance

**Questão**: Qual nível de conformidade regulatória deve ser implementado para um sistema de teste que simula operações reais com criptomoedas?

**Impacto**: Pode afetar a arquitetura de armazenamento de dados, processos de KYC/AML, e relatórios de compliance.

**Sugestão**: Implementar conformidade básica com LGPD/GDPR para dados pessoais e preparar arquitetura para futuras integrações com órgãos reguladores.

### Integração com Blockchain Externa

**Questão**: O monitoramento de blockchain deve ser implementado via APIs de terceiros (como Blockchair, BlockCypher) ou via nós próprios?

**Impacto**: Afeta custos operacionais, latência de confirmação de depósitos, e dependências externas.

**Sugestão**: Iniciar com APIs de terceiros para MVP e evoluir para nós próprios em produção.

### Escalabilidade de Transações

**Questão**: Qual deve ser a capacidade máxima de transações por segundo que o sistema deve suportar?

**Impacto**: Determina arquitetura de banco de dados, estratégias de cache, e infraestrutura de deployment.

**Sugestão**: Projetar para 1.000 TPS inicialmente com arquitetura que permita scaling horizontal.

### Conversão de Moedas

**Questão**: O sistema deve implementar conversão automática entre moedas ou manter saldos estritamente separados?

**Impacto**: Adiciona complexidade de integração com APIs de cotação, gerenciamento de spread, e contabilização de ganhos/perdas.

**Sugestão**: Fase 1 com saldos separados, Fase 2 com conversão manual, Fase 3 com conversão automática opcional.

Este documento de arquitetura fornece a base completa para implementação do VisaNetPay como um ecossistema fechado de pagamentos multi-moeda, priorizando segurança, auditabilidade e experiência do usuário.