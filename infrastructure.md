## Passo a passo para criar uma infraestrutura simples na Azure para estudos.

### Passo 1: Criar uma Conta no Azure
1. **Acesse o site do Azure**: Vá para [Azure for Students](https://azure.microsoft.com/pt-br/free/students/)[1](https://azure.microsoft.com/pt-br/free/students/).
2. **Inscreva-se**: Clique em "Comece gratuitamente" e siga as instruções para criar sua conta. Você receberá um crédito gratuito de USD$ 100 e 12 meses de serviços gratuitos.

### Passo 2: Criar um Grupo de Recursos
1. **Acesse o Portal do Azure**: Faça login no portal do Azure.
2. **Criar um Grupo de Recursos**:
   - No menu à esquerda, clique em "Grupos de recursos".
   - Clique em "Adicionar".
   - Preencha os campos necessários, como o nome do grupo e a região.
   - Clique em "Revisar + criar" e depois em "Criar".

### Passo 3: Criar uma Máquina Virtual (VM)
1. **Acesse a seção de Máquinas Virtuais**:
   - No menu à esquerda, clique em "Máquinas virtuais".
   - Clique em "Adicionar" e depois em "Máquina virtual".
2. **Configurar a VM**:
   - Selecione o grupo de recursos que você criou.
   - Dê um nome à sua VM.
   - Escolha a região e a imagem do sistema operacional (por exemplo, Windows Server ou Ubuntu).
   - Selecione o tamanho da VM (para estudos, uma VM de baixo custo como B1s é suficiente).
3. **Configurar a Autenticação**:
   - Escolha o método de autenticação (senha ou chave SSH).
   - Preencha os campos necessários.
4. **Configurar a Rede**:
   - A configuração padrão geralmente é suficiente para estudos.
5. **Revisar e Criar**:
   - Clique em "Revisar + criar".
   - Verifique as configurações e clique em "Criar".

### Passo 4: Conectar-se à Máquina Virtual
1. **Acesse a VM**:
   - No portal do Azure, vá para "Máquinas virtuais" e selecione a VM que você criou.
   - Clique em "Conectar" e siga as instruções para se conectar via RDP (para Windows) ou SSH (para Linux).

### Passo 5: Explorar Outros Serviços
1. **Armazenamento**:
   - No menu à esquerda, clique em "Contas de armazenamento".
   - Clique em "Adicionar" e configure uma nova conta de armazenamento.
2. **Banco de Dados**:
   - No menu à esquerda, clique em "Banco de dados SQL".
   - Clique em "Adicionar" e configure um novo banco de dados SQL.

### Dicas Adicionais
- **Documentação**: Utilize a documentação oficial do Azure para aprofundar seus conhecimentos.
- **Tutoriais**: Explore tutoriais e cursos gratuitos disponíveis no Microsoft Learn.

[1](https://azure.microsoft.com/pt-br/free/students/): [Microsoft Azure for Students](https://azure.microsoft.com/pt-br/free/students/)
