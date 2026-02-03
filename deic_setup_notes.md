# MediaCMS: DEIC向けのドキュメント変更

## 設定変更
`deploy/docker/local_settings.py`で次の変更が必要です：

```python

# default workflow
PORTAL_WORKFLOW = 'private'

# Authentication Settings
# these two are necessary so that users cannot register through system accounts. They can only register through identity providers
REGISTER_ALLOWED = False
USERS_CAN_SELF_REGISTER = False

USE_RBAC = True
USE_SAML = True
USE_IDENTITY_PROVIDERS = True

# Proxy and SSL Settings
USE_X_FORWARDED_HOST = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
SECURE_SSL_REDIRECT = True
CSRF_COOKIE_SECURE = True
SESSION_COOKIE_SECURE = True

# SAML Configuration
SOCIALACCOUNT_ADAPTER = 'saml_auth.adapter.SAMLAccountAdapter'
ACCOUNT_USERNAME_VALIDATORS = "users.validators.less_restrictive_username_validators"
SOCIALACCOUNT_PROVIDERS = {
    "saml": {
        "provider_class": "saml_auth.custom.provider.CustomSAMLProvider",
    }
}
SOCIALACCOUNT_AUTO_SIGNUP = True
SOCIALACCOUNT_EMAIL_REQUIRED = False

# if set to strict, user is created with the email from the saml provider without
# checking if the email is already on the system
# however if this is ommited, and user tries to login with an email that already exists on
# the system, then they get to the ugly form where it suggests they add a username/email/name

ACCOUNT_PREVENT_ENUMERATION = 'strict'

```

## SAML設定手順

### ステップ1: SAMLアイデンティティプロバイダーの追加
1. 管理パネルに移動
2. 「Identity Provider」を選択
3. 次のように設定：
   - **Provider**: saml # 必ず小文字で設定してください！
   - **Provider ID**: `wayf.wayf.dk`
   - **IDP Config Name**: `Deic`（または好みの名前）
   - **Client ID**: `wayf_dk`（重要：URLを定義します。例：`https://deic.mediacms.io/accounts/saml/wayf_dk`）
   - **Site**: デフォルトのものを設定

### ステップ2: SAML設定の追加
SAML Configurationsタブで設定できます：

1. **IDP ID**: URLである必要があります。例：`https://wayf.wayf.dk`
2. **IDP Certificate**: SAMLプロバイダーからのx509cert
3. **SSO URL**: `https://wayf.wayf.dk/saml2/idp/SSOService2.php`
4. **SLO URL**: `https://wayf.wayf.dk/saml2/idp/SingleLogoutService.php`
5. **SP Metadata URL**: SPに設定されたメタデータURL。例：`https://deic.mediacms.io/saml/metadata`。これはSPのURLを指している必要があり、自動生成されます

### ステップ3: その他のオプションの設定
1. **メール設定**：
   - `verified_email`: 有効にすると、SAML応答からのメールは確認済みとしてマークされます
   - `Remove from groups`: 有効にすると、IDPでグループから削除された場合、ログイン後にユーザーはグループから削除されます
2. **グローバルロールマッピング**: SAML（SAML設定タブで設定）によって返されたロールをMediaCMSのロールにマッピングします
3. **グループロールマッピング**: SAML（SAML設定タブで設定）によって返されたロールを、ユーザーが追加されるグループのロールにマッピングします
4. **グループマッピング**: このIDPに関連付けられたグループを作成します。SAMLから来るグループIDを、MediaCMSのグループに関連付けます
5. **カテゴリマッピング**: グループID（SAML応答から）をMediaCMSのカテゴリにマッピングします
