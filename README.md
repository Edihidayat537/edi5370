1. Otorisasi

fungsi publik getAuth(){ 
    /** 
     * @param ShopApiShopee $model 
     */ 
    $httpType = ((isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] == 'on') || (isset ($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] == 'https')) 
        ? 'https://' : 'http://'; 
    $redirectUrl = $httpType . $_SERVER['HTTP_HOST'] . '/'; 
    $token = hash('sha256', $this->_ShopApiShopee->shopee_key . $redirectUrl); 
    $url = self::SHOPEE_GET_AUTH_URL 
        . '?id=' . $this->_ShopApiShopee->shopee_shop_id 
        . '&token=' . $tanda 
        . '&arahkan' . $redirectUrl; 
    header("Lokasi : " .$url); 
    $this->_ShopApiShopee->token = $token;
    if (!$this->_ShopApiShopee->save()) { 
        return self::fail(self::CODE_SAVE_ERROR,serialize($this->_ShopApiShopee->getErrors())); 
    } 
}

2. Penyambungan untuk mendapatkan informasi pesanan

fungsi statis getOrderList($shopApiShopee, $days = 1, $offsetDay = 0)
{ 
    if(!empty($shopApiShopee) && $shopApiShopee instanceof ShopApiShopee ) 
    { 
        $to_time_stamp = time() - $offsetDay * 86400; 
        $from_time_stamp = $to_time_stamp - 86400 * $hari; 
        $arr = array( 
            'partner_id' => (int)$shopApiShopee->shopee_partner_id, 
            'shopid' => (int)$shopApiShopee->shopee_shop_id, 
            'timestamp' => time(), 
            'create_time_from' => $from_time_stamp, 
            'create_time_to' => $to_time_stamp, 
            'pagination_offset' => 0, 
            'pagination_entries_per_page' => 100 
        );
        $result = self::_getCurlResponse($shopApiShopee, self::SHOPEE_GET_ORDER_LIST_URL, $arr); 
        kembali $hasil; 
    } 
    else 
    { 
        return self::fail(self::CODE_NO_FIND, 'param shopee tidak valid:' .serialize($shopApiShopee)); 
    } 
}

fungsi statis pribadi _getCurlResponse($shopApiShopee, $url, $arr){ 
    $arr = json_encode($arr); 
    $contentLength = strlen($arr); 
    $strConcat = $url.'|'.$arr; 
    $authorizationKey = hash_hmac('sha256', $strConcat, $shopApiShopee->shopee_key); 
    $header = array( 
        'Content-Type:application/json', 
        "Content-Length:" . $contentLength, 
        'Authorization:' . $authorizationKey 
    ); 
    $params = array( 
        'base_uri' => $url, 
        'headers' => $header, 
        'verify' => false, 
        'body' => $arr 
    ); 
    kembalikan diri::_curl($params);


3. Gunakan curl untuk meminta data orderList

    fungsi statis pribadi _curl($params){ 
        $ch = curl_init($params['base_uri']); 
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE); 
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE); 
        curl_setopt($ch, CURLOPT_HTTPHEADER, $params['headers']); 
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, BENAR); 
// curl_setopt($ch, CURLOPT_HEADER, TRUE); curl_setopt 
        ($ch, CURLOPT_POST, BENAR); 
        curl_setopt($ch, CURLOPT_POSTFIELDS, $params['body']); 
        $str = curl_exec($ch); 
        $status = curl_getinfo($ch, CURLINFO_HTTP_CODE); 
        curl_close($ch); 
        if( $status === self::CODE_SUCCESS ){
            kembalikan self::success(json_decode($str,true), self::CODE_SUCCESS); 
        }else{ 
            mengembalikan self::fail(json_decode($str,true), $status); 
        } 
    }
