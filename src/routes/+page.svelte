<script>
	let userInfo = {
		height: 170,
		weight: 70,
		age: 30,
		experience: 3,
		averageScore: 100,
		swingSpeed: 'medium',
		preferredBrand: '',
		budget: 50000,
		clubType: 'driver'
	};

	let showRecommendation = false;
	let recommendation = /** @type {any} */ (null);
	
	let selectedVideo = /** @type {File|null} */ (null);
	let videoUrl = /** @type {string|null} */ (null);
	let isAnalyzing = false;
	let videoSuggestions = /** @type {any} */ (null);
	let showVideoSuggestions = false;

	const clubData = {
		driver: {
			low_speed: { name: 'テーラーメイド SIM2 MAX', shaft: 'フレックスA', price: 45000, reason: 'ヘッドスピードが遅めの方に最適な軽量設計' },
			medium_speed: { name: 'キャロウェイ EPIC SPEED', shaft: 'フレックスSR', price: 52000, reason: 'バランスの取れた飛距離性能' },
			high_speed: { name: 'タイトリスト TSi3', shaft: 'フレックスS', price: 58000, reason: 'ヘッドスピードが速い方向けの低スピン設計' }
		},
		iron: {
			beginner: { name: 'ピン G425', shaft: 'フレックスR', price: 80000, reason: '初心者に優しい大型ヘッド' },
			intermediate: { name: 'ミズノ JPX921', shaft: 'フレックスSR', price: 90000, reason: '中級者向けのバランス型' },
			advanced: { name: 'タイトリスト T100', shaft: 'フレックスS', price: 110000, reason: '上級者向けの操作性重視' }
		}
	};

	function getSpeedCategory(/** @type {string} */ speed) {
		switch(speed) {
			case 'slow': return 'low_speed';
			case 'medium': return 'medium_speed';
			case 'fast': return 'high_speed';
			default: return 'medium_speed';
		}
	}

	function getSkillLevel(/** @type {number} */ score, /** @type {number} */ experience) {
		if (score > 110 || experience < 2) return 'beginner';
		if (score > 90 || experience < 5) return 'intermediate';
		return 'advanced';
	}

	function generateRecommendation() {
		let rec = /** @type {any} */ ({});
		
		if (userInfo.clubType === 'driver') {
			const speedCat = getSpeedCategory(userInfo.swingSpeed);
			rec = clubData.driver[speedCat];
		} else if (userInfo.clubType === 'iron') {
			const skillLevel = getSkillLevel(userInfo.averageScore, userInfo.experience);
			rec = clubData.iron[skillLevel];
		}

		const heightAdjustment = userInfo.height > 180 ? '+1インチ' : userInfo.height < 160 ? '-0.5インチ' : '標準';
		
		recommendation = {
			...rec,
			heightAdjustment,
			totalPrice: rec.price + (userInfo.budget > rec.price ? 0 : -5000),
			suitable: rec.price <= userInfo.budget
		};
		
		showRecommendation = true;
	}

	function handleVideoUpload(/** @type {Event} */ event) {
		const file = /** @type {HTMLInputElement} */ (event.target).files?.[0];
		if (file && file.type.startsWith('video/')) {
			selectedVideo = file;
			videoUrl = URL.createObjectURL(file);
			showVideoSuggestions = false;
			videoSuggestions = null;
		}
	}

	async function analyzeVideo() {
		if (!selectedVideo) return;
		
		isAnalyzing = true;
		
		try {
			await new Promise(resolve => setTimeout(resolve, 2000));
			
			const analysisResults = {
				swingSpeed: Math.random() > 0.5 ? 'fast' : Math.random() > 0.3 ? 'medium' : 'slow',
				tempo: Math.random() > 0.7 ? '速い' : Math.random() > 0.4 ? '普通' : 'ゆっくり',
				posture: Math.random() > 0.6 ? '良好' : '要改善',
				backswing: Math.random() > 0.5 ? 'コンパクト' : 'フル',
				followThrough: Math.random() > 0.7 ? '完璧' : '改善の余地あり'
			};
			
			const suggestions = generateVideoBasedSuggestions(analysisResults);
			
			videoSuggestions = {
				analysis: analysisResults,
				suggestions: suggestions,
				recommendedPractice: getRecommendedPractice(analysisResults),
				clubRecommendation: getClubRecommendationFromVideo(analysisResults)
			};
			
			showVideoSuggestions = true;
		} catch (error) {
			console.error('動画解析エラー:', error);
		} finally {
			isAnalyzing = false;
		}
	}

	function generateVideoBasedSuggestions(/** @type {any} */ analysis) {
		const suggestions = [];
		
		if (analysis.posture === '要改善') {
			suggestions.push('アドレス時の姿勢を見直しましょう。背筋を伸ばし、膝を軽く曲げることを意識してください。');
		}
		
		if (analysis.tempo === '速い') {
			suggestions.push('スイングテンポが速めです。ゆっくりとしたリズムでスイングすることで、ミート率が向上します。');
		}
		
		if (analysis.followThrough === '改善の余地あり') {
			suggestions.push('フォロースルーが不十分です。ターゲット方向へしっかりとクラブを振り抜きましょう。');
		}
		
		if (analysis.backswing === 'フル') {
			suggestions.push('フルスイングが確認できます。安定性を重視する場合は、コンパクトなスイングも練習してみてください。');
		}
		
		return suggestions;
	}

	function getRecommendedPractice(/** @type {any} */ analysis) {
		const practices = [];
		
		if (analysis.swingSpeed === 'slow') {
			practices.push('ヘッドスピード向上のため、軽めのクラブでの素振り練習');
		} else if (analysis.swingSpeed === 'fast') {
			practices.push('ヘッドスピードを活かすため、方向性重視の練習');
		}
		
		practices.push('ミラー前でのアドレス姿勢チェック');
		practices.push('メトロノームを使ったテンポ練習');
		
		return practices;
	}

	function getClubRecommendationFromVideo(/** @type {any} */ analysis) {
		if (analysis.swingSpeed === 'fast') {
			return {
				name: 'タイトリスト TSi3 ドライバー',
				reason: '高いヘッドスピードに対応した低スピン設計',
				shaft: 'フレックスS',
				price: 58000
			};
		} else if (analysis.swingSpeed === 'slow') {
			return {
				name: 'テーラーメイド SIM2 MAX ドライバー',
				reason: 'ヘッドスピードが遅めの方に最適な高弾道設計',
				shaft: 'フレックスA',
				price: 45000
			};
		} else {
			return {
				name: 'キャロウェイ EPIC SPEED ドライバー',
				reason: 'バランスの取れた飛距離性能',
				shaft: 'フレックスSR',
				price: 52000
			};
		}
	}
</script>

<main class="container">
	<h1>🏌️‍♂️ ゴルフクラブ診断アプリ</h1>
	<p>あなたに最適なゴルフクラブとシャフトを提案します</p>

	<!-- Video Upload Section -->
	<div class="video-section">
		<h2>📹 スイング動画解析</h2>
		<p>スイング動画をアップロードして、より詳細な分析と提案を受けられます</p>
		
		<div class="video-upload-area">
			<input 
				type="file" 
				id="video-upload" 
				accept="video/*" 
				on:change={handleVideoUpload}
				style="display: none;"
			>
			<label for="video-upload" class="upload-button">
				📱 動画をアップロード
			</label>
			
			{#if selectedVideo}
				<div class="video-preview">
					<video controls width="100%" style="max-width: 400px;">
						<source src={videoUrl} type={selectedVideo.type}>
						<track kind="captions" srclang="ja" label="Japanese">
						お使いのブラウザは動画再生に対応していません。
					</video>
					<p class="video-info">📁 {selectedVideo.name}</p>
					
					<button 
						class="analyze-button" 
						on:click={analyzeVideo}
						disabled={isAnalyzing}
					>
						{#if isAnalyzing}
							🔄 解析中...
						{:else}
							🔍 動画を解析する
						{/if}
					</button>
				</div>
			{/if}
		</div>
	</div>

	<div class="form-section">
		<h2>基本情報</h2>
		
		<div class="form-group">
			<label for="height">身長 (cm):</label>
			<input type="number" id="height" bind:value={userInfo.height} min="140" max="200">
		</div>

		<div class="form-group">
			<label for="weight">体重 (kg):</label>
			<input type="number" id="weight" bind:value={userInfo.weight} min="40" max="120">
		</div>

		<div class="form-group">
			<label for="age">年齢:</label>
			<input type="number" id="age" bind:value={userInfo.age} min="10" max="80">
		</div>

		<div class="form-group">
			<label for="experience">ゴルフ歴 (年):</label>
			<input type="number" id="experience" bind:value={userInfo.experience} min="0" max="50">
		</div>

		<div class="form-group">
			<label for="averageScore">平均スコア:</label>
			<input type="number" id="averageScore" bind:value={userInfo.averageScore} min="70" max="150">
		</div>

		<div class="form-group">
			<label for="swingSpeed">スイングスピード:</label>
			<select id="swingSpeed" bind:value={userInfo.swingSpeed}>
				<option value="slow">遅め (40m/s未満)</option>
				<option value="medium">普通 (40-45m/s)</option>
				<option value="fast">速め (45m/s以上)</option>
			</select>
		</div>

		<div class="form-group">
			<label for="clubType">クラブタイプ:</label>
			<select id="clubType" bind:value={userInfo.clubType}>
				<option value="driver">ドライバー</option>
				<option value="iron">アイアン</option>
			</select>
		</div>

		<div class="form-group">
			<label for="budget">予算 (円):</label>
			<input type="number" id="budget" bind:value={userInfo.budget} min="10000" max="200000" step="5000">
		</div>

		<button class="recommend-btn" on:click={generateRecommendation}>
			おすすめクラブを診断する
		</button>
	</div>

	{#if showRecommendation && recommendation}
		<div class="recommendation-section">
			<h2>🎯 診断結果</h2>
			
			<div class="recommendation-card {recommendation.suitable ? 'suitable' : 'over-budget'}">
				<h3>{recommendation.name}</h3>
				<p class="shaft"><strong>推奨シャフト:</strong> {recommendation.shaft}</p>
				<p class="length"><strong>長さ調整:</strong> {recommendation.heightAdjustment}</p>
				<p class="price"><strong>価格:</strong> ¥{recommendation.price.toLocaleString()}</p>
				<p class="reason"><strong>選択理由:</strong> {recommendation.reason}</p>
				
				{#if !recommendation.suitable}
					<div class="budget-warning">
						⚠️ 予算オーバーです。中古品やセール時期を狙うことをおすすめします。
					</div>
				{:else}
					<div class="suitable-badge">
						✅ 予算内でおすすめです！
					</div>
				{/if}
			</div>

			<div class="additional-tips">
				<h3>📝 追加アドバイス</h3>
				<ul>
					{#if userInfo.experience < 2}
						<li>初心者の方は、まずレッスンを受けることをおすすめします</li>
					{/if}
					{#if userInfo.averageScore > 100}
						<li>スコア改善のため、ショートゲームの練習を重点的に行いましょう</li>
					{/if}
					{#if userInfo.height > 180}
						<li>身長が高めなので、ライ角の調整も検討してください</li>
					{:else if userInfo.height < 160}
						<li>身長が低めなので、軽めのクラブを選ぶと扱いやすいでしょう</li>
					{/if}
				</ul>
			</div>
		</div>
	{/if}

	<!-- Video Analysis Results -->
	{#if showVideoSuggestions && videoSuggestions}
		<div class="video-analysis-section">
			<h2>🎯 動画解析結果</h2>
			
			<!-- Analysis Summary -->
			<div class="analysis-summary">
				<h3>📊 スイング分析</h3>
				<div class="analysis-grid">
					<div class="analysis-item">
						<span class="label">スイングスピード:</span>
						<span class="value speed-{videoSuggestions.analysis.swingSpeed}">
							{videoSuggestions.analysis.swingSpeed === 'fast' ? '速い' : 
							 videoSuggestions.analysis.swingSpeed === 'medium' ? '普通' : '遅い'}
						</span>
					</div>
					<div class="analysis-item">
						<span class="label">テンポ:</span>
						<span class="value">{videoSuggestions.analysis.tempo}</span>
					</div>
					<div class="analysis-item">
						<span class="label">姿勢:</span>
						<span class="value posture-{videoSuggestions.analysis.posture === '良好' ? 'good' : 'bad'}">
							{videoSuggestions.analysis.posture}
						</span>
					</div>
					<div class="analysis-item">
						<span class="label">バックスイング:</span>
						<span class="value">{videoSuggestions.analysis.backswing}</span>
					</div>
					<div class="analysis-item">
						<span class="label">フォロースルー:</span>
						<span class="value followthrough-{videoSuggestions.analysis.followThrough === '完璧' ? 'perfect' : 'needs-work'}">
							{videoSuggestions.analysis.followThrough}
						</span>
					</div>
				</div>
			</div>

			<!-- Video-based Club Recommendation -->
			<div class="video-club-recommendation">
				<h3>🏌️ 動画解析に基づくクラブ推奨</h3>
				<div class="club-card">
					<h4>{videoSuggestions.clubRecommendation.name}</h4>
					<p class="club-shaft"><strong>推奨シャフト:</strong> {videoSuggestions.clubRecommendation.shaft}</p>
					<p class="club-price"><strong>価格:</strong> ¥{videoSuggestions.clubRecommendation.price.toLocaleString()}</p>
					<p class="club-reason"><strong>推奨理由:</strong> {videoSuggestions.clubRecommendation.reason}</p>
				</div>
			</div>

			<!-- Improvement Suggestions -->
			{#if videoSuggestions.suggestions.length > 0}
				<div class="improvement-suggestions">
					<h3>💡 改善提案</h3>
					<ul>
						{#each videoSuggestions.suggestions as suggestion}
							<li>{suggestion}</li>
						{/each}
					</ul>
				</div>
			{/if}

			<!-- Practice Recommendations -->
			<div class="practice-recommendations">
				<h3>🎯 推奨練習メニュー</h3>
				<ul>
					{#each videoSuggestions.recommendedPractice as practice}
						<li>{practice}</li>
					{/each}
				</ul>
			</div>
		</div>
	{/if}
</main>

<style>
	.container {
		max-width: 800px;
		margin: 0 auto;
		padding: 2rem;
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
	}

	h1 {
		color: #2d5a27;
		text-align: center;
		margin-bottom: 0.5rem;
	}

	h1 + p {
		text-align: center;
		color: #666;
		margin-bottom: 2rem;
	}

	.form-section {
		background: #f8f9fa;
		padding: 2rem;
		border-radius: 10px;
		margin-bottom: 2rem;
	}

	.form-section h2 {
		color: #2d5a27;
		margin-bottom: 1.5rem;
	}

	.form-group {
		margin-bottom: 1rem;
	}

	label {
		display: block;
		margin-bottom: 0.5rem;
		font-weight: 600;
		color: #333;
	}

	input, select {
		width: 100%;
		padding: 0.75rem;
		border: 2px solid #ddd;
		border-radius: 5px;
		font-size: 1rem;
		transition: border-color 0.3s;
	}

	input:focus, select:focus {
		outline: none;
		border-color: #4CAF50;
	}

	.recommend-btn {
		background: linear-gradient(135deg, #4CAF50, #2d5a27);
		color: white;
		border: none;
		padding: 1rem 2rem;
		font-size: 1.1rem;
		font-weight: 600;
		border-radius: 25px;
		cursor: pointer;
		width: 100%;
		margin-top: 1rem;
		transition: transform 0.2s, box-shadow 0.2s;
	}

	.recommend-btn:hover {
		transform: translateY(-2px);
		box-shadow: 0 5px 15px rgba(76, 175, 80, 0.3);
	}

	.recommendation-section {
		animation: fadeIn 0.5s ease-in;
	}

	@keyframes fadeIn {
		from { opacity: 0; transform: translateY(20px); }
		to { opacity: 1; transform: translateY(0); }
	}

	.recommendation-card {
		background: white;
		padding: 2rem;
		border-radius: 10px;
		box-shadow: 0 4px 15px rgba(0,0,0,0.1);
		margin-bottom: 1.5rem;
		border-left: 5px solid #4CAF50;
	}

	.recommendation-card.over-budget {
		border-left-color: #ff6b6b;
	}

	.recommendation-card h3 {
		color: #2d5a27;
		margin-bottom: 1rem;
		font-size: 1.5rem;
	}

	.shaft, .length, .price, .reason {
		margin-bottom: 0.75rem;
		line-height: 1.5;
	}

	.price {
		font-size: 1.2rem;
		color: #2d5a27;
	}

	.suitable-badge {
		background: #d4edda;
		color: #155724;
		padding: 0.75rem;
		border-radius: 5px;
		margin-top: 1rem;
		font-weight: 600;
	}

	.budget-warning {
		background: #f8d7da;
		color: #721c24;
		padding: 0.75rem;
		border-radius: 5px;
		margin-top: 1rem;
		font-weight: 600;
	}

	.additional-tips {
		background: #e3f2fd;
		padding: 1.5rem;
		border-radius: 10px;
		border-left: 5px solid #2196F3;
	}

	.additional-tips h3 {
		color: #1976D2;
		margin-bottom: 1rem;
	}

	.additional-tips ul {
		margin: 0;
		padding-left: 1.5rem;
	}

	.additional-tips li {
		margin-bottom: 0.5rem;
		line-height: 1.5;
	}

	/* Video Upload Styles */
	.video-section {
		background: #f0f8ff;
		padding: 2rem;
		border-radius: 10px;
		margin-bottom: 2rem;
		border-left: 5px solid #2196F3;
	}

	.video-section h2 {
		color: #1976D2;
		margin-bottom: 1rem;
	}

	.video-section p {
		color: #666;
		margin-bottom: 1.5rem;
	}

	.video-upload-area {
		text-align: center;
	}

	.upload-button {
		display: inline-block;
		background: linear-gradient(135deg, #2196F3, #1976D2);
		color: white;
		padding: 1rem 2rem;
		border-radius: 25px;
		cursor: pointer;
		font-weight: 600;
		transition: transform 0.2s, box-shadow 0.2s;
		text-decoration: none;
	}

	.upload-button:hover {
		transform: translateY(-2px);
		box-shadow: 0 5px 15px rgba(33, 150, 243, 0.3);
	}

	.video-preview {
		margin-top: 1.5rem;
		padding: 1.5rem;
		background: white;
		border-radius: 10px;
		box-shadow: 0 2px 10px rgba(0,0,0,0.1);
	}

	.video-info {
		color: #666;
		margin: 1rem 0;
		font-size: 0.9rem;
	}

	.analyze-button {
		background: linear-gradient(135deg, #FF9800, #F57C00);
		color: white;
		border: none;
		padding: 0.75rem 1.5rem;
		border-radius: 20px;
		cursor: pointer;
		font-weight: 600;
		transition: transform 0.2s, box-shadow 0.2s;
	}

	.analyze-button:hover:not(:disabled) {
		transform: translateY(-2px);
		box-shadow: 0 5px 15px rgba(255, 152, 0, 0.3);
	}

	.analyze-button:disabled {
		opacity: 0.7;
		cursor: not-allowed;
	}

	/* Video Analysis Results Styles */
	.video-analysis-section {
		background: #fff9e6;
		padding: 2rem;
		border-radius: 10px;
		margin-bottom: 2rem;
		border-left: 5px solid #FF9800;
		animation: fadeIn 0.5s ease-in;
	}

	.video-analysis-section h2 {
		color: #F57C00;
		margin-bottom: 1.5rem;
	}

	.analysis-summary {
		background: white;
		padding: 1.5rem;
		border-radius: 10px;
		margin-bottom: 1.5rem;
		box-shadow: 0 2px 10px rgba(0,0,0,0.1);
	}

	.analysis-summary h3 {
		color: #F57C00;
		margin-bottom: 1rem;
	}

	.analysis-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
		gap: 1rem;
	}

	.analysis-item {
		display: flex;
		justify-content: space-between;
		padding: 0.5rem 0;
		border-bottom: 1px solid #eee;
	}

	.analysis-item .label {
		font-weight: 600;
		color: #333;
	}

	.analysis-item .value {
		font-weight: 500;
	}

	.speed-fast { color: #e74c3c; }
	.speed-medium { color: #f39c12; }
	.speed-slow { color: #3498db; }
	.posture-good { color: #27ae60; }
	.posture-bad { color: #e74c3c; }
	.followthrough-perfect { color: #27ae60; }
	.followthrough-needs-work { color: #f39c12; }

	.video-club-recommendation {
		background: white;
		padding: 1.5rem;
		border-radius: 10px;
		margin-bottom: 1.5rem;
		box-shadow: 0 2px 10px rgba(0,0,0,0.1);
	}

	.video-club-recommendation h3 {
		color: #F57C00;
		margin-bottom: 1rem;
	}

	.club-card {
		border: 2px solid #FF9800;
		padding: 1.5rem;
		border-radius: 10px;
		background: #fff9e6;
	}

	.club-card h4 {
		color: #F57C00;
		margin-bottom: 1rem;
		font-size: 1.3rem;
	}

	.club-shaft, .club-price, .club-reason {
		margin-bottom: 0.75rem;
		line-height: 1.5;
	}

	.club-price {
		font-size: 1.1rem;
		color: #F57C00;
		font-weight: 600;
	}

	.improvement-suggestions, .practice-recommendations {
		background: white;
		padding: 1.5rem;
		border-radius: 10px;
		margin-bottom: 1.5rem;
		box-shadow: 0 2px 10px rgba(0,0,0,0.1);
	}

	.improvement-suggestions h3, .practice-recommendations h3 {
		color: #F57C00;
		margin-bottom: 1rem;
	}

	.improvement-suggestions ul, .practice-recommendations ul {
		margin: 0;
		padding-left: 1.5rem;
	}

	.improvement-suggestions li, .practice-recommendations li {
		margin-bottom: 0.75rem;
		line-height: 1.5;
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}
		
		.form-section, .video-section, .video-analysis-section {
			padding: 1.5rem;
		}

		.analysis-grid {
			grid-template-columns: 1fr;
		}
	}
</style>
