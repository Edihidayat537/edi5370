2018年8月之后，shopee开始使用新接口，需要进行授权操作

1.授权

public function getAuth(){
    /**
     * @param ShopApiShopee $model
     */
    $httpType = ((isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] == 'on') || (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] == 'https'))
        ? 'https://' : 'http://';
    $redirectUrl = $httpType . $_SERVER['HTTP_HOST'] . '/';
    $token = hash('sha256', $this->_ShopApiShopee->shopee_key . $redirectUrl);
    $url = self::SHOPEE_GET_AUTH_URL
        . '?id=' . $this->_ShopApiShopee->shopee_shop_id
        . '&token=' . $token
        . '&redirect=' . $redirectUrl;
    header("Location: " . $url);
    $this->_ShopApiShopee->token = $token;
    if (!$this->_ShopApiShopee->save()) {
        return self::fail(self::CODE_SAVE_ERROR,serialize($this->_ShopApiShopee->getErrors()));
    }
}
