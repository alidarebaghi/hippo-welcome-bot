
<?php
$bot_token = '7935747327:AAEWki3t8g60wdIlpnsIDdNfgOKCLERQjk4';
$channel_username = '@Hippocoffee';

function sendMessage($chat_id, $text, $keyboard = null) {
    global $bot_token;
    $url = "https://api.telegram.org/bot$bot_token/sendMessage";
    $post_fields = [
        'chat_id' => $chat_id,
        'text' => $text,
        'parse_mode' => 'HTML'
    ];
    if ($keyboard) {
        $post_fields['reply_markup'] = json_encode(['keyboard' => $keyboard, 'resize_keyboard' => true]);
    }
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_HTTPHEADER, ["Content-Type:application/json"]);
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($post_fields));
    curl_exec($ch);
}

$content = file_get_contents("php://input");
$update = json_decode($content, true);

$chat_id = $update["message"]["chat"]["id"];
$text = $update["message"]["text"];

if ($text == "/start") {
    sendMessage($chat_id, "برای دریافت کد تخفیف ویژه، لطفاً اول عضو کانال ما شوید و سپس روی دکمه «عضو شدم» بزنید.", [
        [["text" => "✅ عضویت در کانال", "url" => "https://t.me/Hippocoffee"]],
        [["text" => "🔁 عضو شدم"]]
    ]);
} elseif ($text == "🔁 عضو شدم") {
    $url = "https://api.telegram.org/bot$bot_token/getChatMember?chat_id=$channel_username&user_id=$chat_id";
    $result = json_decode(file_get_contents($url), true);

    if ($result["result"]["status"] == "member" || $result["result"]["status"] == "creator" || $result["result"]["status"] == "administrator") {
        sendMessage($chat_id, "خوش آمدید به دنیای قهوه هیپو ☕️
اینجا قهوه تو مستقیم از کارخانه میگیری 🏭

🎁اینم کد تخفیف ویژه فقط برای خودت 👇
telhippo

ازت دعوت میکنم حتما به محصولات مون یک نگاهی بندازی😊❤️
https://hippo.coffee");
    } else {
        sendMessage($chat_id, "😕 هنوز عضو کانال نشدی!
لطفاً اول عضو کانال شو و بعد روی «عضو شدم» بزن.");
    }
}
?>
