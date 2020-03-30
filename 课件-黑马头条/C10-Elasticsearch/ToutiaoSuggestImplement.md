# 头条suggest查询实现

### 思路

1. 先将关键字在completions 自动补全索引库中查询，获取建议的补全信息
2. 如没有获取到补全信息，可能表示用户输入的关键词有拼写错误，在articles索引库中进行纠错建议查询

### 实现

在toutiao-backend/toutiao/resources/search.py中实现自动补全视图

```python
class SuggestionResource(Resource):
    """
    联想建议
    """
    def get(self):
        """
        获取联想建议
        """
        qs_parser = RequestParser()
        qs_parser.add_argument('q', type=inputs.regex(r'^.{1,50}$'), required=True, location='args')
        args = qs_parser.parse_args()
        q = args.q

        # 先尝试自动补全建议查询
        query = {
            'from': 0,
            'size': 10,
            '_source': False,
            'suggest': {
                'word-completion': {
                    'prefix': q,
                    'completion': {
                        'field': 'suggest'
                    }
                }
            }
        }
        ret = current_app.es.search(index='completions', body=query)
        options = ret['suggest']['word-completion'][0]['options']
        
        # 如果没得到查询结果，进行纠错建议查询
        if not options:
            query = {
                'from': 0,
                'size': 10,
                '_source': False,
                'suggest': {
                    'text': q,
                    'word-phrase': {
                        'phrase': {
                            'field': '_all',
                            'size': 1
                        }
                    }
                }
            }
            ret = current_app.es.search(index='articles', doc_type='article', body=query)
            options = ret['suggest']['word-phrase'][0]['options']

        results = []
        for option in options:
            if option['text'] not in results:
                results.append(option['text'])

        return {'options': results}
```

