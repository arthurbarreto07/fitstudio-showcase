# FitStudio — plataforma de gestão para studios fitness

**Área do aluno + painel administrativo** para studios e academias pequenas que trabalham com aulas
em turma com vaga limitada. O aluno marca a própria aula vendo a vaga real, e o acesso à agenda
segue o plano dele. O dono acompanha o dia, os alunos e os vencimentos em um painel.

> Sistema que desenvolvi para studios e academias de aula em turma. **Código-fonte privado**:
> esta página mostra o que ele faz. As telas usam marca, pessoas e dados fictícios.
> Acesso ao código para avaliação técnica sob solicitação.

<p align="center">
  <img src="docs/prints/aluno-inicio.png" width="230" alt="Início da área do aluno">
  <img src="docs/prints/aluno-agendar.png" width="230" alt="Agendamento de aula">
  <img src="docs/prints/aluno-aulas.png" width="230" alt="Minhas aulas">
</p>

## O problema

Em studio pequeno, agendamento e controle de plano acontecem no WhatsApp: o professor confirma
horário na mão, lembra quem está devendo e libera o acesso de quem está em dia. O FitStudio tira isso
do WhatsApp: o aluno agenda sozinho em três passos e o painel mostra quem está com plano vencido.

## Funcionalidades

**Área do aluno (pensada para o celular)**
- Login por CPF, cadastro e recuperação de senha por código enviado por e-mail
- Agenda com **vaga real por horário**, limite semanal conforme o plano e prazo para cancelar/remarcar
- Plano do aluno com data de acesso e vencimento
- Resumo do mês: aulas concluídas, agendadas, canceladas e taxa de presença
- Contrato e política de privacidade aceitos no app (LGPD)

**Painel do studio**
- Visão do dia: próxima turma, quem confirmou, ocupação da grade e quem está com plano vencido
- Agenda por dia com os alunos de cada turma, encaixe e desmarcação
- Grade fixa da semana com capacidade por turma
- Alunos, liberação/bloqueio manual de acesso, congelamento de plano, vencimentos e log de ações

| Painel do studio | Agenda do dia |
|---|---|
| ![Visão do dia](docs/prints/admin-visao.png) | ![Agenda](docs/prints/admin-agenda.png) |
| **Alunos** | **Grade fixa** |
| ![Alunos](docs/prints/admin-alunos.png) | ![Grade](docs/prints/admin-grade.png) |

## Arquitetura

```mermaid
flowchart LR
  A[Aluno no celular] --> F[Front: HTML/CSS/JS<br/>sem build, CSP estrita]
  S[Dono do studio] --> F
  F -->|HTTP + cookie httpOnly| API[API Node.js + Express]
  API --> DB[(Supabase / Postgres<br/>RLS + constraints)]
  API --> EM[Resend<br/>e-mail]
```

- **Nada é decidido no navegador.** O status do plano é lido do banco a cada requisição: um aluno
  com plano vencido ou bloqueado perde o acesso na hora, sem esperar o token expirar.
- **Sessão segura:** JWT curto em cookie httpOnly, refresh token rotativo guardado só como hash.
- **Concorrência no banco:** a disputa pela última vaga é resolvida por constraint no Postgres.

## Stack

`Node.js` · `Express` · `Supabase (Postgres)` · `Zod` · `JWT` · `bcrypt` · `Helmet` ·
`Resend` · `HTML/CSS/JS (módulos ES)` · `Vercel` · `Docker` · `Caddy`

## O que eu aprendi

- Regra de negócio no servidor, sempre: autorização, limite semanal e vaga decididos no backend.
- Concorrência de verdade: resolver a disputa pela última vaga no banco, e não na tela.
- Sessão segura na prática e tempo de resposta constante no login para não revelar se um CPF existe.
- LGPD, migrações versionadas e testes de ponta a ponta antes de mudar produção.

---

Feito por **Arthur Barreto** — estudante de Ciência da Computação (UFVJM) · [github.com/arthurbarreto07](https://github.com/arthurbarreto07)

© 2026 Arthur Barreto. Todos os direitos reservados.
