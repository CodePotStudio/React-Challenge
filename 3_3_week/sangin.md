### Lambda 를 통한 React SSR 적용 

참고 사이트
- https://aws.amazon.com/ko/blogs/compute/building-server-side-rendering-for-react-in-aws-lambda/
= https://github.com/aws-samples/react-ssr-lambda (데모)
- https://medium.com/medwing-engineering-product-design/using-lambda-edge-for-server-side-rendering-318d9422d76b
- https://seoyeonhwng.medium.com/aws-lambda%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-44df535d5487



#### Lambda 를 통한 SSR 을 리서치하게 된 이유 

- 복잡한 서버 설정 없이 AWS Lambda 와 같은 기능을 이용해서 SSR 을 적용할 수는 없을까 하여 이 부분을 찾아보게 되었습니다. 

- 기존에 진행하던 프로젝트를 NextJS 로 변환하는 것은 대대적인 작업이고 다른 스펙 들과 동시에 진행하기가 어려운 부분이 있음

- 서버 비용을 절감하고 상황에 따라서 스케일링 할 수 있는 장점이 있음 



#### 그럼 Lambda 란 무엇인가?

- AWS 에서 제공하는 서버리스 컴퓨팅 플랫폼이다. 

- 서버가 없는 것이 아니라 서버의 존재 자체를 신경 쓸 필요가 없다. 그냥 기능에 필요한 함수만 AWS 람다에 적용해 두면 AWS 서버에서 알아서 다 해주기 때문에 사용한 컴퓨팅 자원, 용량에 대해서만 
  비용을 지불하면 된다. 
  
- 계속 실행시키는 코드가 아니라 특정한 시기에 실행시키는 경우 람다가 유용하다. 



#### React Labmda SSR 에 관한 AWS 문서 

- 이 방법은 S3, CloudFront, API Gateway, Lambda, Lambda@Edge 를 사용합니다.
 
1. 리액트 앱이 S3 Bucket에 CloudFront 와 함께 호스트되어 있습니다. 백엔드는 API Gateway 를 통해서 실행되고 Lambda Function 으로 구현되어 있습니다. 애플리케이션은 클라이언트에게 다운로드되어서 웹브라우저에 렌더링 될 때 Request 를 전송하게 됩니다. 

![image](https://user-images.githubusercontent.com/27527229/114284779-3c707000-9a8d-11eb-9ef5-aaa422a04aa3.png)

2. 리액트 앱이 Lambda Function 과 함께 렌더링 됩니다. CloudFront 배포는 /ssr 로 부터 오는 request를 API Gateway 로 포워드합니다. 이것이 Lambda Function 을 호출하게 되고 이것이 백엔드에서 데이터를 가져와서 HTML Page 를 데이터와 함께
   구성될 수있게 합니다. 이 페이지는 캐시되어서 다음 request 를 최적화 할 수 있습니다. 

![image](https://user-images.githubusercontent.com/27527229/114284831-9f620700-9a8d-11eb-8080-4470a5cad534.png)

3. 리액트 앱은 Lambda@Edge 와 함꼐 렌더링 됩니다. 이것은 위 과정과 거의 동일하지만 /edgessr 을 통해서 이루어지고 Lambda@Edge 로 처리됩니다. 

![image](https://user-images.githubusercontent.com/27527229/114284912-38911d80-9a8e-11eb-9175-8f8f2070ce41.png)



### AWS Lambda@Edge 예시

![image](https://user-images.githubusercontent.com/27527229/114284384-768c4280-9a8a-11eb-8d26-8e8095c0bd2f.png)


- 람다를 통해서 Response 를 이용해 캐싱등을 이용할 수 있어 퍼포먼스 처리를 할 수 있다. 


```javascript
resource "aws_cloudfront_distribution" "distribution" {
  # other configs.....

  # serve assets from s3 directly because we don't need server side rendersing for assets
  ordered_cache_behavior {
    path_pattern     = "/*.*" # files general path pattern
    allowed_methods  = ["GET", "HEAD", "OPTIONS"]
    cached_methods   = ["GET", "HEAD"]
    target_origin_id = local.host_name # S3 bucket name

    forwarded_values {
      # Disable query string forwarding to s3 origin because it's useless in static assets case.
      query_string = false

      cookies {
        forward = "none"
      }
    }

    min_ttl                = 0
    default_ttl            = 3600
    max_ttl                = 86400
    compress               = true
    viewer_protocol_policy = "redirect-to-https"
  }

  # all other pathes will be served from lambda
  ordered_cache_behavior {
    path_pattern     = "/*" # any other path
    allowed_methods  = ["GET", "HEAD", "OPTIONS"]
    cached_methods   = ["GET", "HEAD"]
    target_origin_id = local.host_name # S3 bucket name

    forwarded_values {
      query_string = true

      cookies {
        forward = "none"
      }
    }

    min_ttl                = 0
    default_ttl            = 60
    max_ttl                = 900
    compress               = true
    viewer_protocol_policy = "redirect-to-https"

    # lambda access 
    lambda_function_association {
      event_type   = "origin-request"
      lambda_arn   = aws_lambda_function.ssr_lambda.qualified_arn # Lambda ARN with code version.
      include_body = true # Send CF Request body to lambda 
    }
  }
}

```

#### 람다 엣지와 KOA 서버 맵핑 

- Lambda Edge Request 와 Koa Request 는 포맷 차이가 존재하여서 serverless-http 를 이용한다. 

```javascript

import serverless from 'serverless-http'
import { parseLambdaHeaders } from '../lib/lambdaHelpers'
import { KoaRouter } from '../koaRouter'

const handler = serverless(KoaRouter);

export const run = async (event, context, callback) => {
  const cfEvent = event.Records[0].cf; // CloudFront request object 
  
  // excuting the server code with Koa and passing the request path, headers...
  // here it runs the server side rendering and return the hyderated react app static html
  const response = await handler({...event, ...cfEvent}, context);  

  const buffer = zlib.gzipSync(response.body); // compressing response body
  const body = buffer.toString('base64'); // converting response body to base64

  // parsing the response header to lambda Edge format and setting content-encoding to equal gzip
  const headers = parseLambdaHeaders(response.headers); 

  // returning lambda response to cloud front to cache it.
  callback(null, {
    status: response.statusCode.toString(),
    statusDescription: 'OK',
    bodyEncoding: 'base64',
    headers,
    body
  });
};

export default run;

```

#### WebPack server-build 최적화 

- ECS6 서버에서 돌리기 위해서 VanillaJS 코드로 변환되어야 하여 웹팩을 이용할 것이다. 

- 웹팩을 이용하여 코드 사이즈를 줄이면 배포 시간 및 데이터 비용이 절감 될 수 있다. 

- React Script 를 이용하면 Hash 값이 매번 랜덤으로 생성되고 리액트 애플리케이션이 브라우저에 돌아갈 수 있게 구성되지만 람다에서는 이용할 수 없고, 
  다른 entry point 를 가져야 한다. 

```javascript

const webpack = require('webpack');
const path = require('path');
const paths = require('./paths');

const getClientEnvironment = require('./env'); // load client env from .env to injected in the server-build in build time
const clientManifest = require('../../build/asset-manifest.json'); // reactapp generated assets mappings after client build 

const env = getClientEnvironment(process.env.PUBLIC_URL); 

function config(entry, outputFileName) {
  return {
    entry,

    target: 'node',

    externals: [],

    output: {
      path: paths.serverBuild, // server build output dir
      filename: outputFileName, // server build output filename
      libraryTarget: 'commonjs',  // preffered to use commonjs because you want to refer to lambda.run for example as an entry point for the lambda 
    },

    module: {
      rules: [
        {
          oneOf: [
            {
              test: /\.(js|jsx|mjs)$/,
              use: 'babel-loader',
            },
            {
              test: /\.(svg|jpeg|jpg|gif|png)$/,
              use: [
                {
                  loader: 'file-loader',
                  options: {
                    publicPath: url => url, // override the default publicPath to return the file utl as it's
                    emitFile: false, // don't emit assets, just use them as it is from the client build
                    name(resourcePath) {
                      const filename = path.basename(resourcePath); // get file name from the path
                      const absolutePath = clientManifest.files[`static/media/${filename}`]; // load the current file url from client generated paths

                      return absolutePath;
                    },
                  },
                },
              ],
            },
            {
              test: /\.(scss|css)$/,
              use: 'ignore-loader', // ignore loader for css and scss because they are handled in the client build
            }
          ],
        },
      ],
    },
    plugins: [new webpack.DefinePlugin(env.stringified)],
  };
}

module.exports = {config};
```



