---
tilte: aligo 实现 walk 功能
date: 2023-06-12 19:42:16
tags: [python, aligo]
---



aligo 实现类似 os.walk 的功能。

实测并不实用，仅当玩具。



```python
from aligo import Aligo, Auth, GetShareFileListRequest

Auth._EMAIL_HOST = ''
Auth._EMAIL_PORT = '465'
Auth._EMAIL_USER = ''
Auth._EMAIL_PASSWORD = ''


class WmAligo(Aligo):
    def __init__(self, **options):
        super().__init__(**options)

    def _walk(self, source_dirs, root_file_data):
        result_files = []
        result_dirs = []

        if not root_file_data.get('file_id'):
            files = self.get_file_list()
        else:
            files = self.get_file_list(root_file_data.get('file_id'))

        for file in files:
            if file.type == "folder":
                result_dirs.append(file)
                source_dirs.append({
                    'file_id': file.file_id,
                    'path': root_file_data.get('path') + file.name + '/'
                })
                continue

            if file.type == "file":
                result_files.append(file)

        return [root_file_data.get('file_id'), result_files, result_dirs, root_file_data.get('path')]

    def _walk_share(self, root_share_token, source_dirs, root_file_data):
        result_files = []
        result_dirs = []

        files = self.get_share_file_list_by_share_id(parent_file_id=root_file_data.get('file_id'),
                                                     share_token=root_share_token)

        for file in files:
            if file.type == "folder":
                result_dirs.append(file)
                source_dirs.append({
                    'file_id': file.file_id,
                    'path': root_file_data.get('path') + file.name + '/'
                })
                continue

            if file.type == "file":
                result_files.append(file)

        return [root_share_token, root_file_data.get('file_id'), result_files, result_dirs, root_file_data.get('path')]

    def walk(self, file_id=None):
        if file_id:
            dir_file = self.get_file(file_id)
            if not dir_file:
                raise Exception("file_id: {} 目录不存在".format(file_id))

            if dir_file.type != "folder":
                raise Exception("file_id: {} 文件非目录".format(file_id))
            source_dirs = [
                {
                    'file_id': file_id,
                    'path': '/'
                }
            ]
        else:
            source_dirs = [
                {
                    'file_id': None,
                    'path': '/'
                }
            ]

        index = 0

        while len(source_dirs) > index:
            dir_file_id = source_dirs[index]
            yield self._walk(source_dirs, dir_file_id)
            index = index + 1

    def get_share_file_list_by_share_id(self, parent_file_id=None, share_id=None, share_token=None):

        if not share_token and share_id:
            share_token = self.get_share_token(share_id)

        if not share_token:
            raise Exception("缺失 share_token")

        if parent_file_id:
            body = GetShareFileListRequest(share_id=share_token.share_id, parent_file_id=parent_file_id)
            return self.get_share_file_list(body=body, parent_file_id=parent_file_id, share_token=share_token)

        body = GetShareFileListRequest(share_id=share_token.share_id)
        return self.get_share_file_list(body=body, share_token=share_token)

    def walk_share(self, share_id, file_id=None):

        index = 0

        source_dirs = [
            {
                'file_id': file_id,
                'path': '/'
            }
        ]
        share_token_root = self.get_share_token(share_id)

        if not share_token_root:
            raise Exception("获取 token root 失败")

        while len(source_dirs) > index:
            dir_file_data = source_dirs[index]

            result = self._walk_share(share_token_root, source_dirs, dir_file_data)

            yield result
            index = index + 1

    def get_download_url_str(self, file=None, file_id=None):
        if file and not file_id:
            file_id = file.file_id

        dl_data = self.get_download_url(file_id=file_id)
        return dl_data.url

```

