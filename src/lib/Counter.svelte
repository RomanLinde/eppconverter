<script>
  var result = []
  var alltxt = ""

  function download(data, filename, type) {
        var file = new Blob([data], {type: type});
        var a = document.createElement("a"),
                  url = URL.createObjectURL(file);
        a.href = url;
        a.download = filename;
        document.body.appendChild(a);
        a.click();
        setTimeout(function() {
              document.body.removeChild(a);
              window.URL.revokeObjectURL(url);  
        }, 0);          
      }

      async function loadTxt() {
         var f = document.querySelector("#file");
         var freader = new FileReader();
         freader.onloadend = x => {            
            document.querySelector("#content").innerHTML = x.target.result.toString();
         }
         // @ts-ignore
         freader.readAsText(f.files[0]);
      }      
		function loadData() {         
         var tables = document.querySelectorAll("table.data_table");
         tables.forEach(element => {
            var rows = element.querySelectorAll("tbody tr");
            rows.forEach(r => {               
               var cells = r.querySelectorAll("td.td_data, td.td_data_center, td.td_data_right")
               if(cells.length!=7)
                  return;
                var nazwa = 
                  cells[2].querySelector("i").childNodes[0].nodeValue.trim();
               const el = { 
                  data: cells[0].childNodes[0].nodeValue, 
                  nazwa: nazwa, 
                  numer: cells[3].childNodes[0].childNodes[0].nodeValue.substring(0,27),
                  brutto: parseFloat(cells[4].childNodes[0].nodeValue),
                  datapl: cells[5].childNodes[0].nodeValue, 
               };
               result.push(el);
            })
         });         

         console.log(result)

         var linia = "";
         function addText(str) {
            if (linia!="")
            linia += ",";
            linia += "\""+str+"\"";
         }
         function add(val) {
            if (linia!="")
            linia += ",";
            linia += val;
         }
         function addCur(val) {
            if (linia!="")
            linia += ",";
            linia += val.toFixed(4);
         }

         alltxt = "[INFO]\r\n";
         linia = "";
         addText("1.05"); // 1
         add("0"); // 2 - cel = biuro
         add("1250"); // 3 - strona kodowa
         addText("Palabras");// 4- program
         // nadawca
         add(""); // 5
         add(""); // 6
         add(""); // 7
         add(""); // 8
         add(""); // 9
         add(""); // 10
         add(""); // 11
         // magazyn
         add(""); // 12
         add(""); // 13
         add(""); // 14
         add(""); // 15
         // inne
         add("1"); // 16
         var dataPocz = result[0].data.replaceAll("-","")+"000000"
         add(dataPocz); // 17 - początek
         var dataKon = result[result.length-1].data.replaceAll("-","")+"000000"
         add(dataKon); // 18 - koniec
         add(""); // 19
         add(""); // 20
         add(""); // 21
         add(""); // 22
         add(""); // 23
         add(0); // 24

         alltxt += linia+"\r\n\r\n";
         
         result.forEach(fa => {
            var nr = fa.numer.split("/");
            var data = fa.data.replaceAll("-","")+"000000"
            var dataPl = fa.datapl.replaceAll("-","")+"000000"
            alltxt += "[NAGLOWEK]\r\n";
            linia = "";
            addText("FS"); // 1
            add("1"); // 2
            add("0"); // 3
            add(parseInt(nr[3])) // 4 - numer dokumentu (int)
            add(""); // 5
            add(""); // 6
            addText(fa.numer); // 7
            add(""); // 8
            add(""); // 9
            addText(""); // 10
            add(""); // 11
            add(""); // 12
            addText(fa.nazwa); // 13
            addText(fa.nazwa); // 14
            add(""); // 15
            add(""); // 16
            add(""); // 17
            add(""); // 18
            add(""); // 19
            add(""); // 20
            add(""); // 21
            addText(data); // 22 - wystawienia
            addText(data); // 23 - sprzedaży
            add(""); // 24
            add("1") // 25 - liczba pozycji
            add("0") // 26 - czy od netto
            add("") // 27 
            var netto = Math.floor(fa.brutto * 100 / 1.23) / 100;
            var vat = fa.brutto - netto;
            addCur(netto) // 28  - wartość netto 
            addCur(vat) // 29  - wartość vat
            addCur(fa.brutto) // 30  - wartość brutto
            addCur(0) // 31 - koszt
            addText("0,00"); // 32
            addCur(0); // 33
            addText("PRZELEW"); // 34
            add(dataPl); // 35
            addCur(0); // 36 - kwota zapłacona
            addCur(fa.brutto); // 37 - do zapłaty
            add("0"); // 38
            add("0"); // 39 - zaokrąglanie VAT do 1 gr
            add("1"); // 40 - Automatycznie przeliczana tabela VAT i wartość dokumentu 
            add("0"); // 41
            addText(""); // 42
            addText(""); // 43
            add(""); // 44
            addCur(0); // 45
            addCur(0); // 46
            add("PLN"); // 47 - waluta
            addCur(1); // 48 - kurs waluty
            addText(""); // 49
            add(""); // 50
            add(""); // 51
            add(""); // 52
            add("0"); // 53
            add("0"); // 54
            add("0"); // 55 - transakcja krajowa
            add(""); // 56
            addCur(0); // 57 - kwota pł kartą
            add(""); // 58
            addCur(0); // 59 - pł kredytowa
            add(""); // 60
            add(""); // 61
            add("0"); // 62

            alltxt += linia+"\r\n\r\n";

            alltxt += "[ZAWARTOSC]\r\n"
            linia = "";
            addText("23"); // 1 - nazwa stawki
            addCur(23); // 2 - procent stawki
            addCur(netto); // 3 - netto
            addCur(vat); // 4 - vat
            addCur(fa.brutto); // 5 - brutto

            alltxt += linia+"\r\n\r\n";
         })                           
         console.log(alltxt)
         result = result
      }

      function downloadAll() {
        download(alltxt, "import.epp", "application/binary");
      }

      function select(faktura) {
        result.forEach(x => x.selected = false)
        faktura.selected = true;
        result = result;
      }
</script>

<div id="content" style="visibility: collapse;">
</div>
<form>
   <input id="file" type="file" on:change="{loadTxt}"/>
</form>

Wczytano {result.length} dokumentów:
<button on:click="{loadData}">Wykonaj</button>
{#if result.length>0}
<button on:click="{downloadAll}">Pobierz plik EPP</button>
{/if}

{#each result as faktura }
  <div class="row" class:selected={faktura.selected} title="Kliknij aby zaznaczyć i skopiować do schowka." 
    on:click="{() =>select(faktura)}">
    <p>{faktura.numer}</p>    
    <p>{faktura.nazwa}</p>    
  </div>
{/each}

<style>
  .row {
    background-color: rgb(161, 161, 161);
    color: white;
    font-size: large;
    padding: 5px;
    margin-bottom: 5px;
    cursor: pointer;
  }
  .selected {
    background-color: brown;
    color: black;
  }
</style>
