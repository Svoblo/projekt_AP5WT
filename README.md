# projekt_AP5WT
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# index.html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Aplikace pro práci nad databází zákazníků</title>

    <style>
        /* Styly pro navigační lištu */
        .navbar {
            position: fixed;
            top: 0;
            width: 100%;
            background-color: #333333;
            padding: 10px;
            color: #ffffff;
            text-align: left;
        }

        .navbar a {
            color: #ffffff;
            text-decoration: none;
            margin-right: 10px;
        }
    </style>

    <style>
        body {
            text-align: center;
        }

        h1 {
            margin-top: 50px;
        }

        p {
            margin-bottom: 20px;
        }

        .button {
            margin-bottom: 10px;
        }
    </style>
</head>
<body>
    <div class="navbar">
        <a href="uvod.html">Úvod</a>
        <a href="podrobnosti.php">Zákazníci</a>
        <a href="vyhledavani.php">Vyhledávání</a>
        <a href="novy_zakaznik.php">Přidat nového zákazníka</a>
        <a href="trideny_vypis.php">Tříděný výpis</a>
    </div>
    <br><br>
    <h1>Aplikace pro práci nad databází zákazníků</h1>

    <p>Jednoduchá aplikace umožňuje zobrazit data zákazníků, upravit stávající zákazníky, přidat/smazat záznamy a hledání v databázi podle zvoleného kritéria.</p>

    <p>Pokud databáze neexistuje, je možné ji vytvořit a naplnit daty pomocí níže uvedeného tlačítka.</p>

    <a href="vytvoreni_databaze.php"><button class="button">Vytvořit databázi</button></a>

</body>
</html>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# novy_zakaznik.php
<?php
// Připojení k databázi MySQL
$servername = "localhost";
$username = "root";
$password = "root";
$dbname = "zakaznici";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
    die("Připojení k databázi selhalo: " . $conn->connect_error);
}

// Funkce pro ošetření vstupních dat
function test_input($data)
{
    $data = trim($data);
    $data = stripslashes($data);
    $data = htmlspecialchars($data);
    return $data;
}

// Proměnná pro uchování chyb při validaci
$errors = [];

// Zpracování formuláře po odeslání
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    // Validace pole "jmeno"
    if (empty($_POST["jmeno"])) {
        $errors[] = "Jméno je povinné";
    } else {
        $jmeno = test_input($_POST["jmeno"]);
        if (!preg_match('/^[a-zA-ZáčďéěíľňóôřšťúůýžÁČĎÉĚÍĽŇÓÔŘŠŤÚŮÝŽ\s]+$/', $jmeno)) {
            $errors[] = "Jméno může obsahovat pouze písmena a diakritiku";
        }
    }

    // Validace pole "prijmeni"
    if (empty($_POST["prijmeni"])) {
        $errors[] = "Příjmení je povinné";
    } else {
        $prijmeni = test_input($_POST["prijmeni"]);
        if (!preg_match('/^[a-zA-ZáčďéěíľňóôřšťúůýžÁČĎÉĚÍĽŇÓÔŘŠŤÚŮÝŽ\s]+$/', $prijmeni)) {
            $errors[] = "Příjmení může obsahovat pouze písmena a diakritiku";
        }
    }

    // Validace pole "ulice"
    if (empty($_POST["ulice"])) {
        $errors[] = "Ulice je povinná";
    } else {
        $ulice = test_input($_POST["ulice"]);
        if (!preg_match('/^[a-zA-ZáčďéěíľňóôřšťúůýžÁČĎÉĚÍĽŇÓÔŘŠŤÚŮÝŽ\s]+$/', $ulice)) {
            $errors[] = "Ulice může obsahovat pouze písmena a diakritiku";
        }
    }

    // Validace pole "mesto"
    if (empty($_POST["mesto"])) {
        $errors[] = "Město je povinné";
    } else {
        $mesto = test_input($_POST["mesto"]);
        if (!preg_match('/^[a-zA-ZáčďéěíľňóôřšťúůýžÁČĎÉĚÍĽŇÓÔŘŠŤÚŮÝŽ\s]+$/', $mesto)) {
            $errors[] = "Město může obsahovat pouze písmena a diakritiku";
        }
    }

    // Validace pole "cp"
if (empty($_POST["cp"])) {
    $errors[] = "Číslo popisné je povinné";
} else {
    $cp = test_input($_POST["cp"]);
    if (!is_numeric($cp)) {
        $errors[] = "Číslo popisné musí být číselná hodnota";
    }
}

// Validace pole "psc"
if (empty($_POST["psc"])) {
    $errors[] = "PSČ je povinné";
} else {
    $psc = test_input($_POST["psc"]);
    if (!preg_match('/^[0-9]+$/', $psc)) {
        $errors[] = "PSČ může obsahovat pouze čísla";
    }
}

    // Pokud nejsou žádné chyby, uložíme data do databáze
    if (empty($errors)) {
        // Příprava a provedení SQL dotazu
        $sql = "INSERT INTO zakaznici (jmeno, prijmeni, ulice, cp, mesto, psc)
                VALUES ('$jmeno', '$prijmeni', '$ulice', '$cp', '$mesto', '$psc')";

        if ($conn->query($sql) === true) {
            echo "Nový zákazník byl úspěšně přidán";
        } else {
            echo "Chyba při přidávání zákazníka: " . $conn->error;
        }
    }
}

$conn->close();
?>

<!DOCTYPE html>
<html>
<head>
    <title>Nový zákazník</title>
    <style>
        /* Styly pro navigační lištu */
        .navbar {
            position: fixed;
            top: 0;
            width: 100%;
            background-color: #333333;
            padding: 10px;
            color: #ffffff;
        }

        .navbar a {
            color: #ffffff;
            text-decoration: none;
            margin-right: 10px;
        }
    </style>
</head>
<body>
<div class="navbar">
        <a href="index.html">Úvod</a>
        <a href="podrobnosti.php">Zákazníci</a>
        <a href="vyhledavani.php">Vyhledávání</a>
        <a href="novy_zakaznik.php">Přidat nového zákazníka</a>
        <a href="trideny_vypis.php">Tříděný výpis</a>
    </div>
        <br><br>
    <h1>Nový zákazník</h1>

    <?php if (!empty($errors)) { ?>
        <div style="color: red;">
            <?php foreach ($errors as $error) {
                echo $error . "<br>";
            } ?>
        </div>
    <?php } ?>

    <form method="post" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>">
        <label for="jmeno">Jméno: </label>
        <input type="text" name="jmeno" id="jmeno" value="<?php echo isset($jmeno) ? $jmeno : ''; ?>">
        <br><br>
        <label for="prijmeni">Příjmení: </label>
        <input type="text" name="prijmeni" id="prijmeni" value="<?php echo isset($prijmeni) ? $prijmeni : ''; ?>">
        <br><br>
        <label for="ulice">Ulice: </label>
        <input type="text" name="ulice" id="ulice" value="<?php echo isset($ulice) ? $ulice : ''; ?>">
        <br><br>
        <label for="cp">Číslo popisné: </label>
        <input type="text" name="cp" id="cp" value="<?php echo isset($cp) ? $cp : ''; ?>">
        <br><br>
        <label for="mesto">Město: </label>
        <input type="text" name="mesto" id="mesto" value="<?php echo isset($mesto) ? $mesto : ''; ?>">
        <br><br>
        <label for="psc">PSČ: </label>
        <input type="text" name="psc" id="psc" value="<?php echo isset($psc) ? $psc : ''; ?>">
        <br><br>
        <input type="submit" name="submit" value="Proveď">
    </form>
</body>
</html>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# podrobnosti.php
<?php
// Připojení k databázi MySQL
$servername = "localhost";
$username = "root";
$password = "root";
$dbname = "zakaznici";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
    die("Připojení k databázi selhalo: " . $conn->connect_error);
}

// Dotaz na získání záznamů z databáze "zakaznici"
$sql = "SELECT * FROM zakaznici";
$result = $conn->query($sql);

// Získání všech záznamů jako asociativní pole
$zakaznici = array();
if ($result->num_rows > 0) {
    while ($row = $result->fetch_assoc()) {
        $zakaznici[] = $row;
    }
}

$conn->close();
?>

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
    <title>Podrobnosti</title>
    <style>
        /* Styly pro navigační lištu */
        .navbar {
            position: fixed;
            top: 0;
            width: 100%;
            background-color: #333333;
            padding: 10px;
            color: #ffffff;
        }

        .navbar a {
            color: #ffffff;
            text-decoration: none;
            margin-right: 10px;
        }
    </style>
    <style>
        table {
            border-collapse: collapse;
            width: 100%;
        }

        th, td {
            text-align: left;
            padding: 8px;
        }

        th {
            background-color: #f2f2f2;
        }

        tr:nth-child(even) {
            background-color: #f2f2f2;
        }

        .action-buttons {
            display: flex;
            justify-content: space-around;
        }

        .add-customer {
            text-align: right;
        }
    </style>
</head>
<body>
<div class="navbar">
        <a href="index.html">Úvod</a>
        <a href="podrobnosti.php">Zákazníci</a>
        <a href="vyhledavani.php">Vyhledávání</a>
        <a href="novy_zakaznik.php">Přidat nového zákazníka</a>
        <a href="trideny_vypis.php">Tříděný výpis</a>
    </div>
    <br><br>
    <h1>Podrobnosti o zákaznících</h1>
    <table>
        <tr>
            <th>#</th>
            <th>Jméno</th>
            <th>Příjmení</th>
            <th>Ulice</th>
            <th>Číslo popisné</th>
            <th>Město</th>
            <th>PSČ</th>
            <th></th>
        </tr>

        <?php foreach ($zakaznici as $zakaznik) { ?>
            <tr>
                <td><?php echo $zakaznik['id']; ?></td>
                <td><?php echo $zakaznik['jmeno']; ?></td>
                <td><?php echo $zakaznik['prijmeni']; ?></td>
                <td><?php echo $zakaznik['ulice']; ?></td>
                <td><?php echo $zakaznik['cp']; ?></td>
                <td><?php echo $zakaznik['mesto']; ?></td>
                <td><?php echo $zakaznik['psc']; ?></td>
                <td class="action-buttons">
                    <a href="zobrazeni.php?id=<?php echo $zakaznik['id']; ?>">Zobrazit</a>
                    <a href="upraveni.php?id=<?php echo $zakaznik['id']; ?>">Upravit</a>
                    <a href="smazani.php?id=<?php echo $zakaznik['id']; ?>">Smazat</a>

                    
                </td>
            </tr>
        <?php } ?>
    </table>

    <!-- Zde můžete vložit další HTML kód pro stránku "podrobnosti" -->

</body>
</html>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# smazani.php
<?php
if (isset($_GET['id'])) {
    $id = $_GET['id'];
} else {
    // Pokud není předáno ID, přesměrujte na stránku "podrobnosti.php"
    header("Location: podrobnosti.php");
    exit;
}

// Připojení k databázi MySQL
$servername = "localhost";
$username = "root";
$password = "root";
$dbname = "zakaznici";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
    die("Připojení k databázi selhalo: " . $conn->connect_error);
}

if (isset($_GET['confirm']) && $_GET['confirm'] == 'true') {
    // Pokud je potvrzení "Ano", provede se smazání záznamu
    $sql_delete = "DELETE FROM zakaznici WHERE id = '$id'";

    if ($conn->query($sql_delete) === TRUE) {
        echo "Záznam byl úspěšně smazán.";
        // Přesměrování na stránku "podrobnosti.php" po smazání
        header("Location: podrobnosti.php");
        exit;
    } else {
        echo "Chyba při mazání záznamu: " . $conn->error;
    }
}

// Dotaz pro získání informací o záznamu
$sql = "SELECT * FROM zakaznici WHERE id = '$id'";
$result = $conn->query($sql);

if ($result->num_rows > 0) {
    $row = $result->fetch_assoc();

    echo "Opravdu chcete záznam smazat?";
    echo "<br>";
    echo "<br>";
    echo '<a href="smazani.php?id=' . $id . '&confirm=true"> Ano </a>';
    echo '<a href="podrobnosti.php"> Ne </a>';
} else {
    // Pokud záznam s daným ID neexistuje, přesměrujte na stránku "podrobnosti.php"
    header("Location: podrobnosti.php");
    exit;
}

$conn->close();
?>

<!DOCTYPE html>
<html>
<head>
    <title>Smazání</title>
</head>
<body>
    <!-- Další HTML kód pro stránku "smazani.php" -->
</body>
</html>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# upraveni.php
<?php
// Připojení k databázi MySQL
$servername = "localhost";
$username = "root";
$password = "root";
$dbname = "zakaznici";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
    die("Připojení k databázi selhalo: " . $conn->connect_error);
}

// Kontrola, zda byl odeslán formulář
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    // Získání zaslaných hodnot
    $id = $_POST['id'];
    $jmeno = $_POST['jmeno'];
    $prijmeni = $_POST['prijmeni'];
    $ulice = $_POST['ulice'];
    $cp = $_POST['cp'];
    $mesto = $_POST['mesto'];
    $psc = $_POST['psc'];

    $errors = array(); // Pole pro ukládání chybových hlášek

    // Validace vstupních hodnot
    if (empty($jmeno)) {
        $errors[] = "Pole 'jmeno' nesmí být prázdné.";
    } elseif (preg_match('/\d/', $jmeno)) {
        $errors[] = "Pole 'jmeno' nesmí obsahovat čísla.";
    }

    if (empty($prijmeni)) {
        $errors[] = "Pole 'prijmeni' nesmí být prázdné.";
    } elseif (preg_match('/\d/', $prijmeni)) {
        $errors[] = "Pole 'prijmeni' nesmí obsahovat čísla.";
    }

    if (empty($ulice)) {
        $errors[] = "Pole 'ulice' nesmí být prázdné.";
    } elseif (preg_match('/\d/', $ulice)) {
        $errors[] = "Pole 'ulice' nesmí obsahovat čísla.";
    }

    if (empty($cp)) {
        $errors[] = "Pole 'cp' nesmí být prázdné.";
    } elseif (!is_numeric($cp)) {
        $errors[] = "Pole 'cp' smí obsahovat pouze čísla.";
    }

    if (empty($mesto)) {
        $errors[] = "Pole 'mesto' nesmí být prázdné.";
    } elseif (preg_match('/\d/', $mesto)) {
        $errors[] = "Pole 'mesto' nesmí obsahovat čísla.";
    }

    if (empty($psc)) {
        $errors[] = "Pole 'psc' nesmí být prázdné.";
    } elseif (!is_numeric($psc)) {
        $errors[] = "Pole 'psc' smí obsahovat pouze čísla.";
    }

    // Pokud nejsou žádné chyby, provede se aktualizace záznamu v databázi
    if (empty($errors)) {
        $sql = "UPDATE zakaznici SET jmeno='$jmeno', prijmeni='$prijmeni', ulice='$ulice', cp='$cp', mesto='$mesto', psc='$psc' WHERE id=$id";

        if ($conn->query($sql) === TRUE) {
            header("Location: podrobnosti.php?id=$id");
            exit();
        } else {
            $errors[] = "Chyba při aktualizaci záznamu: " . $conn->error;
        }
    }
}

// Získání záznamu zakaznika z databáze
$id = $_GET['id'];

$sql = "SELECT * FROM zakaznici WHERE id=$id";
$result = $conn->query($sql);

if ($result->num_rows > 0) {
    $zakaznik = $result->fetch_assoc();
} else {
    die("Špatné zadání vstupních údajů/údaje nebyly nalezeny.");
}

$conn->close();
?>

<!DOCTYPE html>
<html>
<head>
    <title>Upravení</title>
    <style>
        /* Styly pro tabulku */
    </style>
            <style>
        /* Styly pro navigační lištu */
        .navbar {
            position: fixed;
            top: 0;
            width: 100%;
            background-color: #333333;
            padding: 10px;
            color: #ffffff;
            text-align:left;
        }

        .navbar a {
            color: #ffffff;
            text-decoration: none;
            margin-right: 10px;
        }
    </style>
</head>
<body>
<div class="navbar">
        <a href="index.html">Úvod</a>
        <a href="podrobnosti.php">Zákazníci</a>
        <a href="vyhledavani.php">Vyhledávání</a>
        <a href="novy_zakaznik.php">Přidat nového zákazníka</a>
        <a href="trideny_vypis.php">Tříděný výpis</a>
    </div>
    <br><br>
    <h1>Upravení</h1>

    <form method="POST" action="<?php echo $_SERVER['PHP_SELF']; ?>">
        <input type="hidden" name="id" value="<?php echo $zakaznik['id']; ?>">

        <label for="jmeno">Jméno:</label>
        <input type="text" name="jmeno" value="<?php echo $zakaznik['jmeno']; ?>"><br>

        <label for="prijmeni">Příjmení:</label>
        <input type="text" name="prijmeni" value="<?php echo $zakaznik['prijmeni']; ?>"><br>

        <label for="ulice">Ulice:</label>
        <input type="text" name="ulice" value="<?php echo $zakaznik['ulice']; ?>"><br>

        <label for="cp">Číslo popisné:</label>
        <input type="text" name="cp" value="<?php echo $zakaznik['cp']; ?>"><br>

        <label for="mesto">Město:</label>
        <input type="text" name="mesto" value="<?php echo $zakaznik['mesto']; ?>"><br>

        <label for="psc">PSČ:</label>
        <input type="text" name="psc" value="<?php echo $zakaznik['psc']; ?>"><br>

        <?php if (!empty($errors)) { ?>
            <ul>
                <?php foreach ($errors as $error) { ?>
                    <li><?php echo $error; ?></li>
                <?php } ?>
            </ul>
        <?php } ?>

        <input type="submit" name="submit" value="Proveď">
    </form>


</body>
</html>

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#trideny_vypis.php

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Tridený výpis</title>
    <style>
        /* Styly pro vaši stránku */
        body {
            text-align: center;
        }

        h1 {
            margin-top: 50px;
        }

        hr {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-top: 20px;
        }

        select {
            width: 200px;
            margin: 0 auto;
        }

        .button {
            margin-top: 20px;
        }

        .results {
            margin-top: 50px;
            text-align: left;
        }

        .results table {
            margin: 0 auto;
            border-collapse: collapse;
        }

        .results th,
        .results td {
            padding: 5px 10px;
            border: 1px solid #ccc;
        }
    </style>
        </style>
        <style>
        /* Styly pro navigační lištu */
        .navbar {
            position: fixed;
            top: 0;
            width: 100%;
            background-color: #333333;
            padding: 10px;
            color: #ffffff;
            text-align:left;
        }

        .navbar a {
            color: #ffffff;
            text-decoration: none;
            margin-right: 10px;
        }
    </style>
</head>
<body>
<div class="navbar">
        <a href="index.html">Úvod</a>
        <a href="podrobnosti.php">Zákazníci</a>
        <a href="vyhledavani.php">Vyhledávání</a>
        <a href="novy_zakaznik.php">Přidat nového zákazníka</a>
        <a href="trideny_vypis.php">Tříděný výpis</a>
    </div>
    <h1>Tříděný výpis</h1>

    <hr>

    <form method="POST" action="trideny_vypis.php">
        <label>Třídit podle:</label>
        <select name="polozka">
            <option value="jmeno">Jméno</option>
            <option value="prijmeni">Příjmení</option>
            <option value="ulice">Ulice</option>
            <option value="cp">Číslo popisné</option>
            <option value="mesto">Město</option>
            <option value="psc">PSČ</option>
        </select>

        <input type="submit" class="button" value="Proveď">
    </form>

    <?php
    // Připojení k databázi MySQL
    $servername = "localhost";
    $username = "root";
    $password = "root";
    $dbname = "zakaznici";

    $conn = new mysqli($servername, $username, $password, $dbname);

    if ($conn->connect_error) {
        die("Připojení k databázi selhalo: " . $conn->connect_error);
    }

    // Zpracování formuláře po odeslání
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        $polozka = $_POST["polozka"];

        // Sestavení a provedení dotazu na seřazení záznamů
        $sql = "SELECT * FROM zakaznici ORDER BY $polozka";
        $result = $conn->query($sql);

        // Výpis výsledků
        if ($result->num_rows > 0) {
            echo '<div class="results">';
            echo '<table>';
            echo '<tr>';
            echo '<th>#</th>';
            echo '<th>Jméno</th>';
            echo '<th>Příjmení</th>';
            echo '<th>Ulice</th>';
            echo '<th>Číslo popisné</th>';
            echo '<th>Město</th>';
            echo '<th>PSČ</th>';
            echo '</tr>';

            while ($row = $result->fetch_assoc()) {
                echo '<tr>';
                echo '<td>' . $row['id'] . '</td>';
                echo '<td>' . $row['jmeno'] . '</td>';
                echo '<td>' . $row['prijmeni'] . '</td>';
                echo '<td>' . $row['ulice'] . '</td>';
                echo '<td>' . $row['cp'] . '</td>';
                echo '<td>' . $row['mesto'] . '</td>';
                echo '<td>' . $row['psc'] . '</td>';
                echo '</tr>';
            }

            echo '</table>';
            echo '</div>';
        } else {
            echo '<p>Žádné záznamy nebyly nalezeny.</p>';
        }
    }

    $conn->close();
    ?>
</body>
</html>
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#vyhledavani.php
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hledání záznamu</title>
    <style>
        /* Styly pro vaši stránku */
        body {
            text-align: center;
        }

        h1 {
            margin-top: 50px;
        }

        hr {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-top: 20px;
        }

        input[type="text"] {
            width: 200px;
            margin: 0 auto;
        }

        select {
            width: 200px;
        }

        .button {
            margin-top: 20px;
        }

        .results {
            margin-top: 50px;
            text-align: left;
        }

        .results table {
            margin: 0 auto;
            border-collapse: collapse;
        }

        .results th,
        .results td {
            padding: 5px 10px;
            border: 1px solid #ccc;
        }
        .okno{margin-left:20px;}
    </style>
        <style>
        /* Styly pro navigační lištu */
        .navbar {
            position: fixed;
            top: 0;
            width: 100%;
            background-color: #333333;
            padding: 10px;
            color: #ffffff;
            text-align:left;
        }

        .navbar a {
            color: #ffffff;
            text-decoration: none;
            margin-right: 10px;
        }
    </style>
</head>
<body>
<div class="navbar">
        <a href="index.html">Úvod</a>
        <a href="podrobnosti.php">Zákazníci</a>
        <a href="vyhledavani.php">Vyhledávání</a>
        <a href="novy_zakaznik.php">Přidat nového zákazníka</a>
        <a href="trideny_vypis.php">Tříděný výpis</a>
    </div>
    <br><br>
    <h1>Hledání záznamu</h1>

    <hr>

    <form method="POST" action="vyhledavani.php">
        <label>Hledat podle:</label>
        <select name="polozka">
            <option value=""> </option>
            <option value="jmeno">Jméno</option>
            <option value="prijmeni">Příjmení</option>
            <option value="ulice">Ulice</option>
            <option value="cp">Číslo popisné</option>
            <option value="mesto">Město</option>
            <option value="psc">PSČ</option>
        </select>

        <label>Co hledat?</label>
        <input type="text" class="okno" name="klicove_slovo">

        <input type="submit" class="button" value="Submit">
        <input type="button" class="button" value="Zrušit" onclick="resetForm()">
    </form>

    <?php
    // Připojení k databázi MySQL
    $servername = "localhost";
    $username = "root";
    $password = "root";
    $dbname = "zakaznici";

    $conn = new mysqli($servername, $username, $password, $dbname);

    if ($conn->connect_error) {
        die("Připojení k databázi selhalo: " . $conn->connect_error);
    }

    // Zpracování formuláře po odeslání
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        $polozka = $_POST["polozka"];
        $klicove_slovo = $_POST["klicove_slovo"];

        // Sestavení a provedení dotazu na vyhledávání záznamů
        $sql = "SELECT * FROM zakaznici WHERE $polozka LIKE '%$klicove_slovo%'";
        $result = $conn->query($sql);

        // Výpis výsledků
        if ($result->num_rows > 0) {
            echo '<div class="results">';
            echo '<table>';
            echo '<tr>';
            echo '<th>#</th>';
            echo '<th>Jméno</th>';
            echo '<th>Příjmení</th>';
            echo '<th>Ulice</th>';
            echo '<th>Číslo popisné</th>';
            echo '<th>Město</th>';
            echo '<th>PSČ</th>';
            echo '</tr>';

            while ($row = $result->fetch_assoc()) {
                echo '<tr>';
                echo '<td>' . $row['id'] . '</td>';
                echo '<td>' . $row['jmeno'] . '</td>';
                echo '<td>' . $row['prijmeni'] . '</td>';
                echo '<td>' . $row['ulice'] . '</td>';
                echo '<td>' . $row['cp'] . '</td>';
                echo '<td>' . $row['mesto'] . '</td>';
                echo '<td>' . $row['psc'] . '</td>';
                echo '</tr>';
            }

            echo '</table>';
            echo '</div>';
        } else {
            echo '<p>Žádné záznamy nebyly nalezeny.</p>';
        }
    }

    $conn->close();
    ?>

    <script>
        function resetForm() {
            document.querySelector("form").reset();
        }
    </script>
</body>
</html>
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#vytvoreni_databaze.php

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Vytvoření databáze</title>

    <style>
        body {
            text-align: center;
        }

        h1 {
            margin-top: 50px;
        }
    </style>
       </style>
        <style>
        /* Styly pro navigační lištu */
        .navbar {
            position: fixed;
            top: 0;
            width: 100%;
            background-color: #333333;
            padding: 10px;
            color: #ffffff;
            text-align:left;
        }

        .navbar a {
            color: #ffffff;
            text-decoration: none;
            margin-right: 10px;
        }
    </style>
</head>
<body>
<div class="navbar">
        <a href="index.html">Úvod</a>
        <a href="podrobnosti.php">Zákazníci</a>
        <a href="vyhledavani.php">Vyhledávání</a>
        <a href="novy_zakaznik.php">Přidat nového zákazníka</a>
        <a href="trideny_vypis.php">Tříděný výpis</a>
    </div>
    <h1>Vytvoření databáze</h1>
    <br>

    <?php
    if (isset($_POST['database_name'])) {
        $databaseName = $_POST['database_name'];
        if ($databaseName === 'zakaznici') {
            // Kontrola existence databáze
            $servername = "localhost";
            $username = "root";
            $password = "root";
            $conn = new mysqli($servername, $username, $password);

            if ($conn->connect_error) {
                die("Připojení k databázi selhalo: " . $conn->connect_error);
            }

            $existingDatabases = $conn->query("SHOW DATABASES LIKE 'zakaznici'");

            if ($existingDatabases->num_rows > 0) {
                echo "Databáze 'zakaznici' již existuje. Databázi nevytvářejte.";
            } else {
                // Vytvoření databáze, tabulky a vložení dat
                $sql = "CREATE DATABASE zakaznici";
                if ($conn->query($sql) === TRUE) {
                    $conn->select_db("zakaznici");

                    $createTableSql = "CREATE TABLE zakaznici (
                        id int(10) UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
                        jmeno char(50) NOT NULL,
                        prijmeni char(50) NOT NULL,
                        ulice char(100) NOT NULL,
                        cp int(10) UNSIGNED NOT NULL,
                        mesto char(30) NOT NULL,
                        psc int(10) UNSIGNED NOT NULL
                    )";

                    if ($conn->query($createTableSql) === TRUE) {
                        $insertDataSql = "INSERT INTO zakaznici (jmeno, prijmeni, ulice, cp, mesto, psc)
                        VALUES ('Adam', 'Bernau', 'Bernauova', 100, 'Bernauov', 12345),
                        ('David', 'Davidov', 'Davidova', 200, 'Davidtown', 45678),
                        ('Cecil', 'Cecilov', 'Cecilova', 300, 'Cecilovtown', 65321)";

                        if ($conn->query($insertDataSql) === TRUE) {
                            echo "Databáze a tabulka byly úspěšně vytvořeny a data byla vložena.";
                        } else {
                            echo "Chyba při vkládání dat: " . $conn->error;
                        }
                    } else {
                        echo "Chyba při vytváření tabulky: " . $conn->error;
                    }

                    $conn->close();
                } else {
                    echo "Chyba při vytváření databáze: " . $conn->error;
                }
            }
        } else {
            echo "Databáze se může jmenovat pouze 'zakaznici'.";
        }
    }
    ?>

    <form method="post">
        <p>Zadejte název databáze:</p>
        <input type="text" name="database_name">
        <br><br>
        <button type="submit">Proveď</button>
    </form>
</body>
</html>
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#zobrazeni.php

<?php
// Připojení k databázi MySQL
$servername = "localhost";
$username = "root";
$password = "root";
$dbname = "zakaznici";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
    die("Připojení k databázi selhalo: " . $conn->connect_error);
}

// Získání ID záznamu z URL parametru
$id = $_GET['id'];

// Dotaz na získání konkrétního záznamu z databáze "zakaznici"
$sql = "SELECT * FROM zakaznici WHERE id = $id";
$result = $conn->query($sql);

// Získání záznamu jako asociativní pole
$zakaznik = $result->fetch_assoc();

$conn->close();
?>

<!DOCTYPE html>
<html>
<head>
<style>
        /* Styly pro navigační lištu */
        .navbar {
            position: fixed;
            top: 0;
            width: 100%;
            background-color: #333333;
            padding: 10px;
            color: #ffffff;
            text-align:left;
        }

        .navbar a {
            color: #ffffff;
            text-decoration: none;
            margin-right: 10px;
        }
    </style>
    <title>Zobrazení záznamu</title>
</head>
<body>
<div class="navbar">
        <a href="index.html">Úvod</a>
        <a href="podrobnosti.php">Zákazníci</a>
        <a href="vyhledavani.php">Vyhledávání</a>
        <a href="novy_zakaznik.php">Přidat nového zákazníka</a>
        <a href="trideny_vypis.php">Tříděný výpis</a>
    </div>
    <br><br>
    <h1>Detail zákazníka</h1>

    <table>
        <tr>
            <td>Jméno:</td>
            <td><?php echo $zakaznik['jmeno']; ?></td>
        </tr>
        <tr>
            <td>Příjmení:</td>
            <td><?php echo $zakaznik['prijmeni']; ?></td>
        </tr>
        <tr>
            <td>Ulice:</td>
            <td><?php echo $zakaznik['ulice']; ?></td>
        </tr>
        <tr>
            <td>Číslo popisné:</td>
            <td><?php echo $zakaznik['cp']; ?></td>
        </tr>
        <tr>
            <td>Město:</td>
            <td><?php echo $zakaznik['mesto']; ?></td>
        </tr>
        <tr>
            <td>PSČ:</td>
            <td><?php echo $zakaznik['psc']; ?></td>
        </tr>
    </table>

    <a href="podrobnosti.php">Zpět</a>
</body>
</html>
