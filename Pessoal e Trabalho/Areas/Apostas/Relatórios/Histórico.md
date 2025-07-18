```dataviewjs
const apostas = dv.pages('"Areas/Apostas/Jogos"')
  .where(p => p.jogo && p.data)
  .sort(p => p.data, 'desc');

let totalStake = 0;
let totalLucro = 0;
let totalOdds = 0;
let countOdds = 0;

const linhas = apostas.map(aposta => {
  const dataFormatada = aposta.data ? aposta.data.toFormat("dd/MM/yyyy") : "-";

  const stake = aposta.stake != null 
    ? `R$ ${aposta.stake.toFixed(2)}` 
    : "-";

  const resultado = aposta.resultado?.toLowerCase() ?? "";
  let resultadoTexto = "-";
  let resultadoClasse = "";
  let lucroFormatado = "-";
  let lucroValor = 0;

  // Determina texto e classe para resultado
  if (resultado === "green" || resultado === "ganho") {
    resultadoTexto = "✅ Green";
    resultadoClasse = "resultado-ganho";
    if (aposta.lucro != null) {
      lucroValor = aposta.lucro;
      lucroFormatado = dv.el("span", `R$ ${lucroValor.toFixed(2)}`, {cls: "lucro-positivo"});
    }
  } else if (resultado === "red" || resultado === "perda") {
    resultadoTexto = "❌ Red";
    resultadoClasse = "resultado-perda";
    if (aposta.stake != null) {
      lucroValor = -aposta.stake;
      lucroFormatado = dv.el("span", `-R$ ${aposta.stake.toFixed(2)}`, {cls: "lucro-negativo"});
    }
  } else if (resultado === "void") {
    resultadoTexto = "⚪ Void";
    resultadoClasse = "resultado-void";
    lucroValor = 0;
    lucroFormatado = dv.el("span", "R$ 0,00", {cls: "valor-zero"});
  }

  // Acumula totais
  if (aposta.stake != null) totalStake += aposta.stake;
  totalLucro += lucroValor;

  if (aposta.odd != null && !isNaN(aposta.odd)) {
    totalOdds += aposta.odd;
    countOdds += 1;
  }

  return [
    dataFormatada,
    aposta.esporte ?? "-",
    aposta.jogo ?? "-",
    aposta.mercado ?? "-",
    aposta.odd != null ? aposta.odd.toFixed(2) : "-",
    dv.el("span", stake, {cls: "valor"}),
    dv.el("span", resultadoTexto, {cls: resultadoClasse}),
    lucroFormatado,
    aposta.stake ? (lucroValor / aposta.stake * 100).toFixed(2) + "%" : "-",
    aposta["casa-de-aposta"] ?? "-",
    aposta.estrategia ?? "-",
    aposta.observacoes ?? "-"
  ];
});

// Linha de totais
const roiTotal = totalStake > 0 ? (totalLucro / totalStake * 100).toFixed(2) + "%" : "-";
const mediaOdds = countOdds > 0 ? (totalOdds / countOdds).toFixed(2) : "-";

const totalRow = [
  "**Totais**",
  "",
  "",
  "",
  mediaOdds, // Média das odds
  dv.el("span", `R$ ${totalStake.toFixed(2)}`, {cls: "valor-total"}),
  "",
  dv.el("span", `R$ ${totalLucro.toFixed(2)}`, {cls: totalLucro >= 0 ? "lucro-positivo" : "lucro-negativo"}),
  roiTotal,
  "",
  "",
  ""
];

dv.table(
  ["Data","Esporte ", "Jogo", "Mercado", "Odd", "Stake", "Resultado", "Lucro","ROI", "Casa de Aposta", "Estratégia", "Observações"], 
  [...linhas, totalRow]
);
```
