# MediaCMSのメディアパーミッション

このドキュメントは、メディアファイルの表示、編集、管理を制御するMediaCMSのパーミッションシステムについて説明します。

## 概要

MediaCMSは、メディアアクセスに対するきめ細かな制御を可能にする柔軟なパーミッションシステムを提供します。システムは以下をサポートしています：

1. **基本パーミッション** - パブリック、プライベート、非公開メディア
2. **ユーザー固有のパーミッション** - 特定のユーザーに付与される直接パーミッション
3. **ロールベースアクセス制御（RBAC）** - グループメンバーシップを通じたカテゴリベースのパーミッション

## メディアの状態

すべてのメディアファイルには、基本的な可視性を決定する状態があります：

- **パブリック** - すべての人に表示
- **プライベート** - 所有者と明示的なパーミッションを持つユーザーのみに表示
- **非公開** - 公開リストには表示されませんが、直接リンクでアクセス可能


## ユーザーロール

MediaCMSには、パーミッションに影響するいくつかのユーザーロールがあります：

- **通常ユーザー** - 自分のメディアをアップロードして管理できます
- **高度なユーザー** - 追加機能（設定可能）
- **MediaCMSエディター** - プラットフォーム全体でコンテンツを編集およびレビューできます
- **MediaCMSマネージャー** - 完全な管理機能
- **管理者** - システム全体へのアクセス

## 直接メディアパーミッション

`MediaPermission`モデルは、個々のユーザーに特定のパーミッションを付与できます：

### パーミッションレベル

- **ビューア** - プライベートであってもメディアを表示できます
- **エディター** - メディアのメタデータを表示および編集できます
- **オーナー** - 削除を含む完全な制御

## ロールベースアクセス制御（RBAC）

RBACが有効（`USE_RBAC`設定）の場合、パーミッションはカテゴリとグループを通じて管理できます：

1. カテゴリをRBAC制御としてマークできます
2. ユーザーは特定のロールを持つRBACグループに割り当てられます
3. RBACグループはカテゴリに関連付けられます
4. ユーザーは、そのロールに基づいて、それらのカテゴリ内のメディアに対するパーミッションを継承します

### RBACロール

- **メンバー** - カテゴリ内のメディアを表示できます
- **コントリビューター** - カテゴリ内のメディアを表示および編集できます
- **マネージャー** - カテゴリ内のメディアに対する完全な制御

## パーミッション確認メソッド

Userモデルは、パーミッションを確認するためのいくつかのメソッドを提供します：

```python
# From users/models.py
def has_member_access_to_media(self, media):
    # Check if user can view the media
    # ...

def has_contributor_access_to_media(self, media):
    # Check if user can edit the media
    # ...

def has_owner_access_to_media(self, media):
    # Check if user has full control over the media
    # ...
```

## パーミッションの適用方法

ユーザーがメディアにアクセスしようとすると、システムは次の順序でパーミッションを確認します：

1. メディアはパブリックですか？はいの場合、アクセスを許可します。
2. ユーザーはメディアの所有者ですか？はいの場合、完全なアクセスを許可します。
3. ユーザーはMediaPermissionを通じて直接パーミッションを持っていますか？はいの場合、対応するアクセスレベルを付与します。
4. RBACが有効な場合、ユーザーはカテゴリメンバーシップを通じてアクセスできますか？はいの場合、対応するアクセスレベルを付与します。
5. 上記のいずれでもない場合、アクセスを拒否します。

## メディアの共有

ユーザーは次の方法で他のユーザーとメディアを共有できます：

1. パブリックまたは非公開にする
2. 特定のユーザーに直接パーミッションを付与する
3. RBACグループがアクセスできるカテゴリに追加する

## 実装の詳細

### メディアリスト

メディアをリストする際、システムはパーミッションに基づいてフィルタリングします：

```python
# Simplified example from files/views/media.py
def _get_media_queryset(self, request, user=None):
    # 1. Public media
    listable_media = Media.objects.filter(listable=True)

    if not request.user.is_authenticated:
        return listable_media

    # 2. User permissions for authenticated users
    user_media = Media.objects.filter(permissions__user=request.user)

    # 3. RBAC for authenticated users
    if getattr(settings, 'USE_RBAC', False):
        rbac_categories = request.user.get_rbac_categories_as_member()
        rbac_media = Media.objects.filter(category__in=rbac_categories)

    # Combine all accessible media
    return listable_media.union(user_media, rbac_media)
```

### パーミッション確認

システムはヘルパーメソッドを使用してパーミッションを確認します：

```python
# From users/models.py
def has_member_access_to_media(self, media):
    # First check if user is the owner
    if media.user == self:
        return True

    # Then check RBAC permissions
    if getattr(settings, 'USE_RBAC', False):
        rbac_groups = RBACGroup.objects.filter(
            memberships__user=self,
            memberships__role__in=["member", "contributor", "manager"],
            categories__in=media.category.all()
        ).distinct()
        if rbac_groups.exists():
            return True

    # Then check MediaShare permissions for any access
    media_permission_exists = MediaPermission.objects.filter(
        user=self,
        media=media,
    ).exists()

    return media_permission_exists
```

## ベストプラクティス

1. **デフォルトでプライベート** - 新しいアップロードをデフォルトでプライベートに設定することを検討してください
2. **カテゴリを使用** - より簡単なパーミッション管理のためにメディアをカテゴリに整理します
3. **チーム向けRBAC** - チームコラボレーションシナリオにはRBACを使用します
4. **例外的な直接パーミッション** - 一度限りの共有には直接パーミッションを使用します

## 設定

パーミッションシステムは、いくつかの設定を通じて構成できます：

- `USE_RBAC` - ロールベースアクセス制御の有効/無効

## 結論

MediaCMSは、シンプルな個人メディアライブラリから、きめ細かなアクセス制御を備えた複雑なチームコラボレーションシナリオまで、さまざまなユースケースに対応できる柔軟で強力なパーミッションシステムを提供します。
