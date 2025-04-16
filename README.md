<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Valor por Extenso</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #000000; /* Fundo preto */
            color: #ecf0f1;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 50px;
        }

        .container {
            background-color: #1c1c1c; /* Fundo escuro para a área do formulário */
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
            width: 100%;
            max-width: 600px;
        }

        input, button {
            padding: 12px;
            font-size: 16px;
            margin: 10px 0;
            width: 100%;
            border-radius: 8px;
            border: none;
        }

        input {
            background-color: #ecf0f1;
            color: #2c3e50;
        }

        button {
            background-color: #e74c3c; /* Botão vermelho */
            color: #ecf0f1;
            font-weight: bold;
            cursor: pointer;
        }

        button:hover {
            background-color: #c0392b; /* Tom de vermelho mais escuro */
        }

        #resultado {
            margin-top: 20px;
            font-size: 18px;
            color: #ecf0f1;
            font-weight: bold;
            white-space: pre-line;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>Converter valor para extenso</h2>
    <input type="text" id="valor" placeholder="Digite um valor, ex: 540,00">
    <button onclick="converterValor()">Converter</button>
    <div id="resultado"></div>
</div>

<script>
    function unidades(n) {
        return ["", "um", "dois", "três", "quatro", "cinco", "seis", "sete", "oito", "nove"][n];
    }

    function especiais(n) {
        return ["dez", "onze", "doze", "treze", "quatorze", "quinze", "dezesseis", "dezessete", "dezoito", "dezenove"][n];
    }

    function dezenas(n) {
        return ["", "", "vinte", "trinta", "quarenta", "cinquenta", "sessenta", "setenta", "oitenta", "noventa"][n];
    }

    function centenas(n) {
        return ["", "cento", "duzentos", "trezentos", "quatrocentos", "quinhentos", "seiscentos", "setecentos", "oitocentos", "novecentos"][n];
    }

    function numeroParaExtenso(n) {
        if (n === 100) return "cem";

        let c = Math.floor(n / 100);
        let d = Math.floor((n % 100) / 10);
        let u = n % 10;

        let resultado = "";

        if (c > 0) resultado += centenas(c);

        if (d === 1) {
            resultado += (resultado ? " e " : "") + especiais(u);
        } else {
            if (d > 1) resultado += (resultado ? " e " : "") + dezenas(d);
            if (u > 0) resultado += (resultado ? " e " : "") + unidades(u);
        }

        return resultado;
    }

    function blocoParaExtenso(n, escalaSingular, escalaPlural) {
        if (n === 0) return "";
        if (n === 1) return numeroParaExtenso(n) + " " + escalaSingular;
        return numeroParaExtenso(n) + " " + escalaPlural;
    }

    function extensoCompleto(valor) {
        if (valor === 0) return "zero real";

        const inteiro = Math.floor(valor);
        const centavos = Math.round((valor - inteiro) * 100);

        const bilhões = Math.floor(inteiro / 1_000_000_000);
        const milhões = Math.floor((inteiro % 1_000_000_000) / 1_000_000);
        const milhares = Math.floor((inteiro % 1_000_000) / 1000);
        const centenas = inteiro % 1000;

        let partes = [];

        if (bilhões) partes.push(blocoParaExtenso(bilhões, "bilhão", "bilhões"));
        if (milhões) partes.push(blocoParaExtenso(milhões, "milhão", "milhões"));
        if (milhares) partes.push(milhares === 1 ? "mil" : numeroParaExtenso(milhares) + " mil");
        if (centenas) partes.push(numeroParaExtenso(centenas));

        let resultado = partes.join(" e ");

        resultado += inteiro === 1 ? " real" : " reais";

        if (centavos > 0) {
            resultado += " e " + numeroParaExtenso(centavos) + (centavos === 1 ? " centavo" : " centavos");
        }

        return resultado;
    }

    function converterValor() {
        let input = document.getElementById("valor").value;

        // Substitui separador de milhar e vírgula por ponto decimal
        input = input.replace(".", "").replace(",", ".");

        const valor = parseFloat(input);

        if (isNaN(valor)) {
            document.getElementById("resultado").innerText = "Por favor, digite um valor válido.";
            return;
        }

        const valorFormatado = valor.toLocaleString("pt-BR", {
            style: "currency",
            currency: "BRL",
            minimumFractionDigits: 2
        });

        const extenso = extensoCompleto(valor).toLowerCase();

        const valorDobro = valor * 2;
        const valorDobroFormatado = valorDobro.toLocaleString("pt-BR", {
            style: "currency",
            currency: "BRL",
            minimumFractionDigits: 2
        });
        const extensoDobro = extensoCompleto(valorDobro).toLowerCase();

        // Indenização de R$ 10.000,00 somada ao valor em dobro
        const valorIndenizacao = valorDobro + 10000;
        const valorIndenizacaoFormatado = valorIndenizacao.toLocaleString("pt-BR", {
            style: "currency",
            currency: "BRL",
            minimumFractionDigits: 2
        });
        const extensoIndenizacao = extensoCompleto(valorIndenizacao).toLowerCase();

        const resultadoFinal = `${valorFormatado} (${extenso})\nDobro: ${valorDobroFormatado} (${extensoDobro})\nIndenização: ${valorIndenizacaoFormatado} (${extensoIndenizacao})`;

        document.getElementById("resultado").innerText = resultadoFinal;
    }
</script>

</body>
</html>
