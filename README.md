1.Fix the problem of outputting double quotes

	public void write(String content, boolean preserveSpaces)
			throws IOException {
			
		checkClosed();

		checkInit();

		if (content == null) {
			content = "";
		}

		if (!firstColumn) {
			outputStream.write(userSettings.Delimiter);
		}

		boolean textQualify = userSettings.ForceQualifier;

		if (!preserveSpaces && content.length() > 0) {
			content = content.trim();
		}

		if (!textQualify
				&& userSettings.UseTextQualifier
				&& (content.indexOf(userSettings.TextQualifier) > -1
						|| content.indexOf(userSettings.Delimiter) > -1
						|| (!useCustomRecordDelimiter && (content
								.indexOf(Letters.LF) > -1 || content
								.indexOf(Letters.CR) > -1))
						|| (useCustomRecordDelimiter && content
								.indexOf(userSettings.RecordDelimiter) > -1)
						|| (firstColumn && content.length() > 0 && content
								.charAt(0) == userSettings.Comment) ||
				// check for empty first column, which if on its own line must
				// be qualified or the line will be skipped
				(firstColumn && content.length() == 0))) {
			textQualify = true;
		}

		if (userSettings.UseTextQualifier && !textQualify
				&& content.length() > 0 && preserveSpaces) {
			char firstLetter = content.charAt(0);

			if (firstLetter == Letters.SPACE || firstLetter == Letters.TAB) {
				textQualify = true;
			}

			if (!textQualify && content.length() > 1) {
				char lastLetter = content.charAt(content.length() - 1);

				if (lastLetter == Letters.SPACE || lastLetter == Letters.TAB) {
					textQualify = true;
				}
			}
		}

		if (textQualify) {
			// remove 
			outputStream.write(userSettings.TextQualifier);

			if (userSettings.EscapeMode == ESCAPE_MODE_BACKSLASH) {
				content = replace(content, "" + Letters.BACKSLASH, ""
						+ Letters.BACKSLASH + Letters.BACKSLASH);
				content = replace(content, "" + userSettings.TextQualifier, ""
						+ Letters.BACKSLASH + userSettings.TextQualifier);
			} else {
				
				//replace
				//content = replace(content, "" + userSettings.TextQualifier, ""
				//		+ userSettings.TextQualifier
				//		+ userSettings.TextQualifier);
				
				//now
				content = replace(content, "" + userSettings.TextQualifier, ""
						+ userSettings.TextQualifier);
			}
		} else if (userSettings.EscapeMode == ESCAPE_MODE_BACKSLASH) {
			content = replace(content, "" + Letters.BACKSLASH, ""
					+ Letters.BACKSLASH + Letters.BACKSLASH);
			content = replace(content, "" + userSettings.Delimiter, ""
					+ Letters.BACKSLASH + userSettings.Delimiter);

			if (useCustomRecordDelimiter) {
				content = replace(content, "" + userSettings.RecordDelimiter,
						"" + Letters.BACKSLASH + userSettings.RecordDelimiter);
			} else {
				content = replace(content, "" + Letters.CR, ""
						+ Letters.BACKSLASH + Letters.CR);
				content = replace(content, "" + Letters.LF, ""
						+ Letters.BACKSLASH + Letters.LF);
			}

			if (firstColumn && content.length() > 0
					&& content.charAt(0) == userSettings.Comment) {
				if (content.length() > 1) {
					content = "" + Letters.BACKSLASH + userSettings.Comment
							+ content.substring(1);
				} else {
					content = "" + Letters.BACKSLASH + userSettings.Comment;
				}
			}
		}

		outputStream.write(content);

		// remove 
		//if (textQualify) {
		//	outputStream.write(userSettings.TextQualifier);
		//}

		firstColumn = false;
	}