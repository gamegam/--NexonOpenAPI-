프로젝트 -> app -> src -> main -> assets 폴더 생성 있으면 
nexon-api.svg 추가
```
@Composable
fun MainLoad(modifier: Modifier = Modifier) {
    val context = LocalContext.current
    var load by rememberSaveable { mutableStateOf(false) }
    var director by rememberSaveable { mutableStateOf(false) }

    if (load) {
        // 만약 로고가 끝난후 이동할 함수 (기본 MainActivity) 사용
        MainActivity(modifier)
        return
    }

    val logoAlpha = remember { Animatable(1f) }
    val logoScale = remember { Animatable(1f) }
    val textAlpha = remember { Animatable(0f) }

    val imageLoader = remember {
        ImageLoader.Builder(context)
            .components { add(SvgDecoder.Factory()) }
            .build()
    }

    LaunchedEffect(Unit) {
        delay(500)

        logoAlpha.animateTo(0f, tween(200))
        logoAlpha.animateTo(1f, tween(200))

        logoScale.animateTo(1.08f, tween(500, easing = FastOutSlowInEasing))

        director = true
    }

    if (director) {
        LaunchedEffect(Unit) {
            textAlpha.animateTo(1f, tween(1000))
            delay(2000)
            load = true
        }
    }

    Box(
        modifier = Modifier
            .fillMaxSize()
            .background(Color(0xFF0D0D0D))
            .clickable(
                interactionSource = remember { MutableInteractionSource() },
                indication = null
            ) { load = true }
    ) {

        Text(
            text = "화면을 클릭하여 스킵하기",
            color = Color.Red.copy(alpha = 0.6f),
            fontSize = 16.sp,
            modifier = Modifier
                .align(Alignment.TopCenter)
                .padding(top = 24.dp)
        )

        Column(
            modifier = Modifier.align(Alignment.Center),
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            AsyncImage(
                model = ImageRequest.Builder(context)
                    .data("file:///android_asset/nexon-api.svg")
                    .build(),
                imageLoader = imageLoader,
                contentDescription = null,
                modifier = Modifier
                    .fillMaxWidth(0.7f)
                    .aspectRatio(215f / 13f)
                    .graphicsLayer {
                        alpha = logoAlpha.value
                        scaleX = logoScale.value
                        scaleY = logoScale.value
                    },
                contentScale = ContentScale.Fit
            )

            if (director) {
                Spacer(modifier = Modifier.height(12.dp))
                Text(
                    text = "이용약관을 준수합니다.",
                    color = Color.White.copy(alpha = 0.7f),
                    fontSize = 16.sp,
                    modifier = Modifier.graphicsLayer {
                        alpha = textAlpha.value
                    }
                )
            }
        }
    }
}
